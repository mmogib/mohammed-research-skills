# Reusable Julia Script Patterns

Patterns extracted from the MISTDFPM project for writing experiment and analysis scripts.

---

## 1. ARGS Dispatch Pattern

Every script supports modular execution via command-line arguments. No args = run everything.

```julia
# At the top of the script, after includes and configuration:

const VALID_PARTS = ["all", "small", "mid", "large", "benchmark", "latex", "summary"]
parts = isempty(ARGS) ? ["all"] : lowercase.(ARGS)

# Validate
unknown = setdiff(parts, VALID_PARTS)
if !isempty(unknown)
    println("Unknown parts: $(join(unknown, ", "))")
    println("Valid: $(join(VALID_PARTS, ", "))")
    error("Unknown parts: $(join(unknown, ", "))")
end

run_all = "all" in parts

# Dispatch to functions
if run_all || "small" in parts || "benchmark" in parts
    do_benchmark([1000, 5000, 10000])
end
if run_all || "mid" in parts || "benchmark" in parts
    do_benchmark([20000, 30000, 50000])
end
(run_all || "latex" in parts)   && do_latex()
(run_all || "summary" in parts) && do_summary()
```

**Usage:**
```bash
julia --project=. scripts/50_experiment1.jl              # run everything
julia --project=. scripts/50_experiment1.jl small         # just small dims
julia --project=. scripts/50_experiment1.jl latex summary  # post-process only
```

**Benefits:** Long experiments can be run in stages; post-processing (tables, plots) can be re-run without re-solving.

---

## 2. CSV Accumulation (Tier Merging)

Run benchmark tiers independently; re-running a tier replaces only its rows.

```julia
const RAW_CSV = joinpath(RESULTS_DIR, "experiment_raw.csv")

function do_benchmark(dims)
    # ... run benchmark, get df_new ...

    # Merge with existing results (preserve other dimensions)
    mkpath(RESULTS_DIR)
    if isfile(RAW_CSV)
        old_df = CSV.read(RAW_CSV, DataFrame)
        kept = filter(r -> !(r.dimension in dims), old_df)
        merged = vcat(kept, df_new)
    else
        merged = df_new
    end
    sort!(merged, [:dimension, :problem, :config])
    CSV.write(RAW_CSV, merged)
    println("Results saved: $(RAW_CSV)")
    println("  $(nrow(merged)) rows, dims=$(sort(unique(merged.dimension)))")
end
```

**Benefits:** If a tier fails or you want to re-run with different settings, other tiers are preserved. Post-processing always reads the merged CSV.

---

## 3. Batch Checkpointing with Auto-Resume

For long parameter searches: save after every batch, skip completed configs on restart.

```julia
const BATCH_SIZE = 20

function do_search()
    # Build all_configs (list of SolverConfig)
    # ...

    raw_path = joinpath(RESULTS_DIR, "search_raw.csv")

    # Load existing checkpoint
    done_configs = Set{String}()
    if isfile(raw_path)
        existing_df = CSV.read(raw_path, DataFrame)
        done_configs = Set(unique(existing_df.config))
        println("CHECKPOINT FOUND: $(length(done_configs))/$(length(all_configs)) already saved")
    end

    remaining = filter(c -> c.name ∉ done_configs, all_configs)

    if isempty(remaining)
        println("All configs already completed!")
    else
        n_batches = ceil(Int, length(remaining) / BATCH_SIZE)
        for bi in 1:n_batches
            i_start = (bi - 1) * BATCH_SIZE + 1
            i_end = min(bi * BATCH_SIZE, length(remaining))
            batch = remaining[i_start:i_end]

            batch_df = run_benchmark(batch, problems_by_dim; maxiter=MAXITER, timeout=TIMEOUT)

            # Append to CSV
            if isfile(raw_path)
                CSV.write(raw_path, batch_df; append=true)
            else
                CSV.write(raw_path, batch_df)
            end

            println("CHECKPOINT SAVED: batch $bi/$n_batches complete")
        end
    end

    # Load full results and rank
    df = CSV.read(raw_path, DataFrame)
    # ... ranking and analysis ...
end
```

**Benefits:** Survives crashes, timeouts, and interruptions. Can monitor progress in real-time.

---

## 4. Timestamped Logging (Tee to File)

Log stdout to both console and a timestamped file.

```julia
function setup_logging(script_name::String)
    logdir = joinpath(@__DIR__, "..", "results", "logs")
    mkpath(logdir)

    timestamp = Dates.format(Dates.now(), "yyyymmdd_HHMMss")
    logpath = joinpath(logdir, "log_$(script_name)_$(timestamp).txt")
    logfile = open(logpath, "w")
    original_stdout = stdout

    rd, wr = redirect_stdout()
    tee_task = @async begin
        try
            while !eof(rd)
                data = readavailable(rd)
                write(original_stdout, data)
                write(logfile, data)
                flush(original_stdout)
                flush(logfile)
            end
        catch
        end
    end

    println("Log file: $logpath")
    return (logpath, original_stdout, rd, wr, tee_task, logfile)
end

function teardown_logging(original_stdout, rd, wr, tee_task, logfile, logpath)
    flush(stdout)
    redirect_stdout(original_stdout)
    close(wr)
    wait(tee_task)
    close(rd)
    flush(logfile)
    close(logfile)
    println("Log saved to: $logpath")
end
```

**Usage:** Call `setup_logging` at script start, `teardown_logging` at end.

---

## 5. LaTeX Table Generation from DataFrames

Generate publication-ready LaTeX tables from benchmark DataFrames.

```julia
function generate_latex_table(df, dim; maxiter=5000)
    df_dim = filter(r -> r.dimension == dim, df)
    problems = unique(df_dim.problem)
    configs = unique(df_dim.config)

    lines = String[]
    push!(lines, "\\begin{table}[htbp]")
    push!(lines, "\\centering")
    push!(lines, "\\caption{Numerical results for \$n = $(dim)\$.}")
    push!(lines, "\\label{tab:exp_n$(dim)}")
    push!(lines, "\\footnotesize")

    col_spec = "l" * repeat("r", length(configs))
    push!(lines, "\\begin{tabular}{$col_spec}")
    push!(lines, "\\toprule")

    # Header row
    header = "Problem"
    for c in configs; header *= " & $c"; end
    push!(lines, header * " \\\\")
    push!(lines, "\\midrule")

    # Data rows
    for prob in problems
        row = replace(prob, "_" => "\\_")
        for c in configs
            r = filter(r -> r.problem == prob && r.config == c, df_dim)
            if nrow(r) == 1
                r1 = first(eachrow(r))
                if r1.error
                    row *= " & ERR"
                elseif !r1.converged
                    row *= " & NC"
                else
                    row *= @sprintf(" & %d (%d)", r1.iterations, r1.f_evals)
                end
            else
                row *= " & ---"
            end
        end
        push!(lines, row * " \\\\")
    end

    # Summary rows
    push!(lines, "\\midrule")
    # Convergence rate row
    conv_row = "Conv. rate"
    for c in configs
        df_c = filter(r -> r.config == c && !r.error, df_dim)
        rate = nrow(df_c) > 0 ? mean(df_c.converged) : 0.0
        conv_row *= @sprintf(" & %.2f", rate)
    end
    push!(lines, conv_row * " \\\\")

    push!(lines, "\\bottomrule")
    push!(lines, "\\end{tabular}")
    push!(lines, "\\end{table}")

    return join(lines, "\n")
end
```

---

## 6. Performance Profile Generation

Using `BenchmarkProfiles.jl` with custom styling.

```julia
using Plots, BenchmarkProfiles, Colors

# Define consistent style per solver
const STYLE_COLORS = [
    RGB(0.0, 0.45, 0.70),   # Your algorithm (blue, prominent)
    RGB(0.0, 0.62, 0.45),   # Reference 1 (green)
    RGB(0.58, 0.40, 0.74),  # Reference 2 (purple)
]
const STYLE_LS = [:solid, :solid, :dash]
const STYLE_LW = [2.5, 1.8, 1.5]

function styled_profile(T, names, colors, ls, lw; title="")
    p = performance_profile(
        PlotsBackend(), T, names;
        title=title, xlabel="τ", ylabel="P(r_{p,s} ≤ τ)",
        logscale=true, legend=:bottomright,
        linestyles=ls,
    )
    for (i, s) in enumerate(p.series_list)
        s[:linecolor] = colors[i]
        s[:linewidth] = lw[i]
    end
    return p
end

# Generate profiles for iterations, F-evals, CPU time
for (metric, T, fname) in [
    ("Iterations", T_iters, "exp_iterations.pdf"),
    ("F-evaluations", T_feval, "exp_fevals.pdf"),
    ("CPU Time", T_cpu, "exp_cpu_time.pdf"),
]
    p = styled_profile(T, config_names, STYLE_COLORS, STYLE_LS, STYLE_LW; title="Performance Profile ($metric)")
    savefig(p, joinpath(RESULTS_DIR, fname))
end
```

---

## 7. OAT Sensitivity Sweep Pattern

One-At-a-Time parameter sweeps.

```julia
const PARAM_SWEEPS = [
    # (label, kwarg_symbol, values, base_kwargs)
    ("lambda",    :λ,     [0.0, 0.1, 0.3, 0.5, 0.7, 0.9], (;)),
    ("rho",       :ρ,     [0.1, 0.3, 0.5, 0.7, 0.9],       (;)),
    ("p_choice",  :p_choice, [:F_w, :d_prev, :y],           (;)),  # categorical
    ("ls_type",   :ls_type,  [:constant, :clipped, :capped], (; η=1.0)),
]

for (label, kwsym, values, base_kw) in PARAM_SWEEPS
    configs = [SolverConfig("$(label)=$(v)", merge(base_kw, NamedTuple{(kwsym,)}((v,))))
               for v in values]
    df = run_benchmark(configs, problems_by_dim; maxiter=MAXITER)

    # Tag results for later analysis
    df.parameter .= label
    df.param_value .= [split(r, "="; limit=2)[2] for r in df.config]
    append!(all_results, df)
end
```

---

## 8. LHS Parameter Search Pattern

Latin Hypercube Sampling with categorical combinations.

```julia
const SEARCH_PARAMS = [
    # (label, symbol, lo, hi)
    ("eta",       :η,      0.5,   2.0),
    ("rho",       :ρ,      0.1,   0.9),
    ("kappa",     :κ,      0.1,  10.0),
    ("lambda",    :λ,      0.0,   0.8),
    ("alpha_min", :α_min,  0.5,   5.0),
]
const PARAM_SYMS = [s[2] for s in SEARCH_PARAMS]
const PARAM_RANGES = [(s[3], s[4]) for s in SEARCH_PARAMS]

const CATEGORICAL_CHOICES = [:F_w, :d_prev, :y]  # p_choice values

# Generate LHS samples
samples = latin_hypercube(N_SAMPLES, PARAM_RANGES; seed=42)

# Build configs: each sample × each categorical choice
configs = SolverConfig[]
for pc in CATEGORICAL_CHOICES
    for i in 1:N_SAMPLES
        kw = NamedTuple{Tuple(PARAM_SYMS)}(Tuple(samples[i, :]))
        kw = merge(FIXED_PARAMS, kw, (; p_choice=pc))
        if validate_params(kw)
            push!(configs, SolverConfig("LHS_$(pc)_$i", kw))
        end
    end
end
```

---

## 9. Progress Reporting with ProgressMeter

The `run_benchmark` function uses ProgressMeter with rich status information:

```julia
using ProgressMeter

p = Progress(total; desc="Benchmarking: ", showspeed=true)
next!(p; showvalues=[
    (:dimension, "n=$n  ($dim_idx/$n_dims dims)"),
    (:config, config.name),
    (:problem, get_name(prob)),
    (:last_result, status),
    (:tally, "$(n_conv) conv, $(n_fail) NC, $(n_err) err"),
])
```

---

## 10. Script Header Convention

Every script follows this header structure:

```julia
# XX_script_name.jl
# Brief description of what this script does
#
# Usage:
#   julia --project=. scripts/XX_script_name.jl              # run everything
#   julia --project=. scripts/XX_script_name.jl part1         # run part1 only
#   julia --project=. scripts/XX_script_name.jl part1 part2   # run both
#
# Parts:
#   part1   — description of what part1 does
#   part2   — description of what part2 does
#
# Run from jcode/:
#   julia --project=. scripts/XX_script_name.jl

include("../src/includes.jl")
# Script-only imports (Plots, BenchmarkProfiles, etc.)

# Logging setup
logpath, _orig_stdout, _rd, _wr, _tee_task, _logfile = setup_logging("script_name")

# ... configuration constants ...
# ... helper functions ...
# ... do_partX() functions ...
# ... ARGS dispatch ...

# Logging teardown
teardown_logging(_orig_stdout, _rd, _wr, _tee_task, _logfile, logpath)
```
