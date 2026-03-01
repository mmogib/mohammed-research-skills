# Script Patterns — Composable Code Blocks

Each section below is a self-contained code block. Compose them in order to build a complete script. Replace `{placeholders}` with project-specific values.

---

## 1. Header Comment Block

Always included. Describes what the script does, where output goes, and how to run it.

```julia
# ============================================================================
# s{NN}: {Title}
# ============================================================================
#
# Goal:   {One-line description of what this script does}
# Output: {Where results go, e.g., results/experiment_name/raw.csv, summary.csv}
#
# Usage:
#   julia --project=. scripts/s{NN}_{name}.jl                  # default subset
#   julia --project=. scripts/s{NN}_{name}.jl --all            # all problems
#   julia --project=. scripts/s{NN}_{name}.jl 3-11 --resume    # range, resume
#   julia --project=. scripts/s{NN}_{name}.jl --summary        # post-process
# ============================================================================
```

---

## 2. Load Path / Imports

**Style A (Module Package):**
```julia
push!(LOAD_PATH, joinpath(@__DIR__, "..", "src"))
using {ModuleName}
using Printf, Statistics
```

**Style B (Flat Include):**
```julia
include(joinpath(@__DIR__, "..", "src", "includes.jl"))
using Printf, Statistics
```

Add script-specific packages after the module/include line (e.g., `using ProgressMeter`, `using Plots`).

---

## 3. ARGS Parsing

Standard pattern for flags and problem ID ranges.

```julia
# --- Parse arguments ---
flags = filter(a -> startswith(a, "-"), ARGS)
ids   = filter(a -> !startswith(a, "-"), ARGS)

verbose    = "--verbose" in flags
do_resume  = "--resume" in flags      # include only if resume feature selected
do_summary = "--summary" in flags     # include only if summary feature selected

if "--all" in flags
    prob_ids = collect(1:{N_PROBLEMS})
elseif !isempty(ids)
    prob_ids = Int[]
    for s in ids
        if contains(s, "-")
            parts = split(s, "-")
            append!(prob_ids, parse(Int, parts[1]):parse(Int, parts[2]))
        else
            push!(prob_ids, parse(Int, s))
        end
    end
else
    prob_ids = {DEFAULT_SUBSET}   # representative subset for quick testing
end
```

**Optional: keyword overrides from flags**
```julia
for f in flags
    if startswith(f, "--eps=")
        global FIXED_ε = parse(Float64, split(f, "=")[2])
    elseif startswith(f, "--maxiter=")
        global FIXED_MAXITER = parse(Int, split(f, "=")[2])
    end
end
```

---

## 4. TeeIO Logging Setup

Requires `io_utils.jl` to be available (see infrastructure-patterns.md).

```julia
# --- Setup logging ---
logpath, tee, logfile = setup_logging("{script_name}")
```

---

## 5. Results Directory

Create the output directory for this script.

```julia
results_dir = joinpath(@__DIR__, "..", "results", "{experiment_name}")
mkpath(results_dir)
```

---

## 6. Configuration Constants

Project-specific constants. Adapt names and values to the project.

```julia
# ============================================================================
# Configuration
# ============================================================================

# Algorithm parameters (tuned values or defaults)
const PARAM_A = {value}
const PARAM_B = {value}

# Experiment settings
const N_STARTS   = {50}              # for multi-start scripts
const RNG_SEED   = {42}              # for reproducibility
const FIXED_ε    = {1e-4}
const FIXED_MAXITER = {5000}
```

---

## 7. Adaptive Parameters

Tolerance and iteration limits that depend on problem dimension.

```julia
# Adaptive parameters based on problem dimension
get_ε(n)       = n >= {threshold} ? {relaxed_ε} : {tight_ε}
get_maxiter(n) = n >= {threshold} ? {large_maxiter} : {small_maxiter}
```

---

## 8. CSV Constants and Paths

Define CSV headers as constants. Column names use snake_case.

```julia
raw_csv_path = joinpath(results_dir, "{prefix}_raw.csv")
const RAW_HEADER = "{col1},{col2},{col3},..."
const SUMMARY_HEADER = "{col1},{col2},{col3},..."
```

---

## 9. Summary Mode Block

Two-mode pattern: `--summary` reads raw CSV and produces aggregated output, then exits. Place this BEFORE the main solve loop.

```julia
# ============================================================================
# Summary mode: read raw CSV → generate summary
# ============================================================================

if do_summary
    if !isfile(raw_csv_path)
        println(tee, "ERROR: No raw CSV found at $raw_csv_path")
        teardown_logging(tee, logpath)
        exit(1)
    end

    # Group raw lines by {grouping_key}
    grouped = Dict{Int, Vector{String}}()
    for line in eachline(raw_csv_path)
        startswith(line, "{first_col_name}") && continue   # skip header
        parts = split(line, ","; limit=2)
        isempty(parts) && continue
        key = tryparse(Int, parts[1])
        key === nothing && continue
        push!(get!(grouped, key, String[]), line)
    end

    println(tee, "=" ^ {width})
    println(tee, "{Summary Title}")
    println(tee, "=" ^ {width})

    # --- Print table header ---
    @printf(tee, "%-5s %-14s %4s %4s  %8s  %10s  %10s\n",
            "ID", "Name", "n", "m", "success", "med_iters", "med_v")
    println(tee, "-" ^ {width})

    # --- Aggregate each group ---
    summary_rows = []
    for key in sort(collect(keys(grouped)))
        lines = grouped[key]

        # Parse fields, compute statistics (median, IQR, counts)
        # ... project-specific aggregation logic ...

        # Print row
        # Push to summary_rows
    end

    println(tee, "-" ^ {width})

    # --- Write summary CSV ---
    summary_csv_path = joinpath(results_dir, "{prefix}_summary.csv")
    open(summary_csv_path, "w") do io
        println(io, SUMMARY_HEADER)
        for s in summary_rows
            @printf(io, "{format_string}\n", {fields}...)
        end
    end

    println(tee, "\nSummary saved to: $summary_csv_path")
    teardown_logging(tee, logpath)
    exit(0)
end
```

---

## 10. Resume Support

Read existing CSV to determine what's already done. Two variants:

**Variant A: Resume by problem ID** (for multi-start scripts where each problem has N rows)
```julia
completed = Set{Int}()

if do_resume && isfile(raw_csv_path)
    prob_lines = Dict{Int, Vector{String}}()
    all_lines = String[]
    for line in eachline(raw_csv_path)
        startswith(line, "{first_col}") && continue
        push!(all_lines, line)
        parts = split(line, ","; limit=2)
        isempty(parts) && continue
        pid = tryparse(Int, parts[1])
        pid === nothing && continue
        push!(get!(prob_lines, pid, String[]), line)
    end

    expected = {expected_rows_per_problem}
    partial = Set{Int}()
    for (pid, lines) in prob_lines
        if length(lines) >= expected
            push!(completed, pid)
        else
            push!(partial, pid)
        end
    end

    # Rewrite CSV without partial rows (they'll be re-run cleanly)
    if !isempty(partial)
        println(tee, "RESUME: Removing $(length(partial)) partial problem(s)")
        open(raw_csv_path, "w") do io
            println(io, RAW_HEADER)
            for line in all_lines
                parts = split(line, ","; limit=2)
                pid = tryparse(Int, parts[1])
                pid === nothing && continue
                pid in completed && println(io, line)
            end
        end
    end

    if !isempty(completed)
        println(tee, "RESUME: $(length(completed)) completed, skipping")
    end
end
```

**Variant B: Resume by (key1, key2) pair** (for ablation/parameter sweep scripts)
```julia
completed_pairs = Set{Tuple{Int, Float64}}()

if do_resume && isfile(raw_csv_path)
    for line in eachline(raw_csv_path)
        startswith(line, "{first_col}") && continue
        fields = split(line, ",")
        length(fields) >= {min_fields} || continue
        k1 = tryparse(Int, fields[{col_idx_1}])
        k2 = tryparse(Float64, fields[{col_idx_2}])
        (k1 === nothing || k2 === nothing) && continue
        push!(completed_pairs, (k1, k2))
    end
    if !isempty(completed_pairs)
        println(tee, "RESUME: $(length(completed_pairs)) completed pairs, skipping")
    end
end
```

---

## 11. Main Loop Banner

Print experiment configuration before the solve loop.

```julia
println(tee, "=" ^ {width})
println(tee, "{Experiment Title}")
println(tee, "=" ^ {width})
println(tee, "Parameters: {param_summary}")
println(tee, "Problems: $(prob_ids)")
println(tee, "Resume: $(do_resume ? "enabled ($(length(completed)) done)" : "disabled")")
println(tee)
```

---

## 12. CSV File Opening

Open raw CSV in append mode (resume) or write mode (fresh).

```julia
if do_resume && !isempty(completed)
    raw_io = open(raw_csv_path, "a")
else
    raw_io = open(raw_csv_path, "w")
    println(raw_io, RAW_HEADER)
end
```

---

## 13. Formatted Output Table Header

Aligned column headers for console output.

```julia
println(tee, "-" ^ {width})
@printf(tee, "%-5s %-14s %4s %4s  %8s  %10s  %10s  %8s\n",
        "ID", "Name", "n", "m", "success", "med_iters", "med_v", "elapsed")
println(tee, "-" ^ {width})
```

---

## 14. ProgressMeter

Progress bar for inner loops (e.g., multiple starts per problem).

```julia
using ProgressMeter

prog = Progress(n_total;
    desc  = @sprintf("  Prob %2d %-14s ", prob_id, name),
    barlen = 30,
    showspeed = true,
    enabled = !verbose)   # disable when verbose (avoids conflict)

for (idx, item) in enumerate(items)
    # ... solve ...
    ProgressMeter.update!(prog, idx;
        showvalues = [
            (:optimal, "$n_ok / $idx"),
            (:errors,  n_error),
        ])
end
finish!(prog)
```

---

## 15. Random Feasible Starting Points

Generate random points inside the feasible set.

```julia
"""
    random_feasible_point(K, n, rng)

Generate a uniformly random point in the feasible set K.
"""
function random_feasible_point(K, n::Int, rng::AbstractRNG)
    if K isa LazySets.Hyperrectangle
        lb = LazySets.low(K)
        ub = LazySets.high(K)
        return lb .+ (ub .- lb) .* rand(rng, n)
    elseif K isa LazySets.HPolytope
        # Simplex: Dirichlet(1,...,1) via exponential variates
        raw = -log.(rand(rng, n))
        return raw ./ sum(raw)
    else
        # Fallback: bounding box
        box = LazySets.overapproximate(K, LazySets.Hyperrectangle)
        lb = LazySets.low(box)
        ub = LazySets.high(box)
        return lb .+ (ub .- lb) .* rand(rng, n)
    end
end
```

Adapt the function signature and set types to the project. If not using LazySets, use the project's feasible set representation.

---

## 16. Try-Catch Per Solve

Wrap each solve in try-catch. Write error rows to CSV.

```julia
try
    result = {solve_function}(...)

    # Process result by status
    if result.status == :optimal
        n_ok += 1
    elseif result.status == :maxiter
        n_maxiter += 1
    elseif result.status == :linesearch_failed
        n_lsfail += 1
    end

    # Write raw CSV row and flush
    @printf(raw_io, "{format}\n", {fields}...)
    flush(raw_io)
catch ex
    n_error += 1
    @printf(raw_io, "{error_format}\n", {error_fields}...)
    flush(raw_io)
    @printf(logfile, "  [ERROR] prob %d: %s\n", id, sprint(showerror, ex))
end
```

---

## 17. Per-Iteration History Recording

Save convergence data for later figure generation.

```julia
# In the solve call, enable history recording:
result = {solve_function}(...; record_history=true)

# After solve, write history CSV:
if !isempty(result.history)
    param_str = replace(@sprintf("%.2f", param_val), "." => "p")
    hfile = joinpath(hist_dir, "hist_$(id)_param$(param_str).csv")
    open(hfile, "w") do io
        println(io, "iter,{metric_name},{other_metric}")
        for h in result.history
            @printf(io, "%d,%.10e,%.10e\n", h.iter, h.metric, h.other)
        end
    end
end
```

---

## 18. Elapsed Time Formatting

Human-readable elapsed time string.

```julia
function format_elapsed(elapsed)
    if elapsed < 60
        return @sprintf("%.0fs", elapsed)
    elseif elapsed < 3600
        return @sprintf("%.1fm", elapsed / 60)
    else
        return @sprintf("%.1fh", elapsed / 3600)
    end
end
```

---

## 19. Main Loop Structure

The overall loop pattern. Compose with the blocks above.

```julia
t_script_start = time()
n_done = 0

for prob_id in prob_ids
    # Resume: skip completed
    if prob_id in completed
        @printf(tee, "%-5d (SKIPPED)\n", prob_id)
        continue
    end

    # Load problem
    {prob, F, JF = get_problem(prob_id)}
    t_prob = time()

    # Configure
    cfg = {ConfigType}(
        ε = get_ε(prob.n),
        maxiter = get_maxiter(prob.n),
    )

    # Inner loop (starts, parameter values, etc.)
    n_ok = 0; n_error = 0
    for (idx, item) in enumerate(items)
        # ... try-catch solve block ...
    end

    # Per-problem summary line
    prob_elapsed = time() - t_prob
    n_done += 1
    @printf(tee, "%-5d %-14s %4d %4d  %3d/%3d   %8s\n",
            prob_id, name, n, m, n_ok, n_total, format_elapsed(prob_elapsed))
end

close(raw_io)
println(tee, "-" ^ {width})
total_elapsed = time() - t_script_start
@printf(tee, "\nDone. %d problems in %s\n", n_done, format_elapsed(total_elapsed))
```

---

## 20. Teardown

Always at the end of the script.

```julia
println(tee, "Raw results: $raw_csv_path")
println(tee, "\nRun with --summary to generate aggregated report.")
println(tee, "=" ^ {width})
teardown_logging(tee, logpath)
```

---

## 21. Figure Script Pattern

Minimal structure for figure generation scripts. No ARGS, no TeeIO.

```julia
using Plots
using Printf
using LaTeXStrings

# Backend selection
gr()          # or: pgfplotsx() for LaTeX-native PDF

# ============================================================================
# Configuration
# ============================================================================

const DATA_DIR = joinpath(@__DIR__, "..", "results", "{data_source}")
const FIGS_DIR = joinpath(@__DIR__, "..", "results", "figures")
mkpath(FIGS_DIR)
const OUTPUT_PATH = joinpath(FIGS_DIR, "{figure_name}.pdf")

# Plot styling
const STYLES = [:solid, :dash, :dashdot, :dot]
const COLORS = [:royalblue, :firebrick, :forestgreen, :darkorange]

# ============================================================================
# Read data
# ============================================================================

function read_data(path)
    # Read CSV, return arrays for plotting
end

# ============================================================================
# Build figure
# ============================================================================

subplots = []
for (id, name) in PROBLEMS
    p = plot(;
        xlabel = "{x_label}",
        ylabel = "{y_label}",
        title = name,
        yscale = :log10,          # if appropriate
        legend = :topright,
        legendfontsize = 7,
        titlefontsize = 10,
        guidefontsize = 9,
        tickfontsize = 8,
        grid = true,
        framestyle = :box,
    )

    for (i, variant) in enumerate(variants)
        # Read and plot data
        plot!(p, xs, ys;
            label = labels[i],
            color = COLORS[i],
            linestyle = STYLES[i],
            linewidth = 1.5,
        )
    end
    push!(subplots, p)
end

fig = plot(subplots...;
    layout = (1, length(subplots)),
    size = (400 * length(subplots), 350),
    left_margin = 5Plots.mm,
    bottom_margin = 5Plots.mm,
)

savefig(fig, OUTPUT_PATH)
println("Saved: $OUTPUT_PATH")
```

---

## 22. Shifted Geometric Mean

Aggregation metric that reduces influence of very small values. Used in OAT/LHS scripts.

```julia
"""
    shifted_geom_mean(vals, shift)

SGM(x; s) = (∏(xᵢ + s))^{1/n} - s
"""
function shifted_geom_mean(vals::Vector{Float64}, shift::Float64)
    isempty(vals) && return NaN
    n = length(vals)
    log_sum = sum(log(v + shift) for v in vals)
    return exp(log_sum / n) - shift
end
```
