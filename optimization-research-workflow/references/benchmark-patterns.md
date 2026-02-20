# Benchmarking Infrastructure Patterns

Patterns for multi-solver benchmarking, performance profiles, and comparison tables.

---

## 1. SolverConfig Pattern

Uniform interface for constructing any solver with any parameters.

```julia
struct SolverConfig
    name::String
    kwargs::NamedTuple
    constructor::Any      # (F, C; x0, kwargs...) -> solver
end

# Backward-compatible: defaults to your main algorithm
SolverConfig(name::String, kwargs::NamedTuple) = SolverConfig(name, kwargs, MyAlgorithm)

# Usage
configs = [
    SolverConfig("MyAlgo",      (;),                    MyAlgorithm),
    SolverConfig("MyAlgo-v2",   (; λ=0.5),              MyAlgorithm),
    SolverConfig("Reference1",  (;),                    Reference1),
    SolverConfig("Reference2",  (; proj_type=:inexact),  Reference2),
]
```

---

## 2. BenchmarkResult

Standard result type collecting all metrics from a single solve.

```julia
struct BenchmarkResult
    config_name::String
    problem_name::String
    dimension::Int
    iterations::Int
    converged::Bool
    final_residual::Float64
    fw_iterations::Int        # Frank-Wolfe projection iterations
    cpu_time::Float64
    f_evals::Int
    mean_ls_bt::Float64       # Mean line search backtracks
    max_ls_bt::Int
    mean_step_size::Float64
    error::Bool
    error_msg::String
end
```

---

## 3. run_single Pattern

Run one solver on one problem with full error handling.

```julia
function run_single(config::SolverConfig, prob::Problem;
                    maxiter=5000, stag_window=50, stag_tol=0.99,
                    div_factor=1e6, timeout=0.0, rel_change_tol=0.0)
    pname = get_name(prob)
    n = get_dimension(prob)
    try
        F = get_F(prob)
        C = get_C(prob)
        x0 = get_x0(prob)

        solver = config.constructor(F, C; x0=x0, config.kwargs...)
        reset(solver.it)
        reset(solver.f_evals)
        elapsed = @elapsed begin
            x, k, converged = solve(solver; maxiter=maxiter,
                stag_window=stag_window, stag_tol=stag_tol,
                div_factor=div_factor, timeout=timeout,
                rel_change_tol=rel_change_tol)
        end

        final_res = (x !== nothing) ? norm(F(x)) : NaN
        fw_iters = value(solver.it)
        n_fevals = value(solver.f_evals)
        ls_stats = ls_summary(solver.ls_tracker)

        return BenchmarkResult(config.name, pname, n, k, converged,
            final_res, fw_iters, elapsed, n_fevals,
            ls_stats.mean_bt, ls_stats.max_bt, ls_stats.mean_step,
            false, "")
    catch e
        return BenchmarkResult(config.name, pname, n, -1, false, NaN,
            0, 0.0, 0, 0.0, 0, 0.0, true, sprint(showerror, e))
    end
end
```

**Key features:**
- Catches all exceptions → never crashes the benchmark loop.
- Records error flag + message for post-hoc analysis.
- Tracks CPU time, F-evaluations, line search statistics.
- Supports timeout, stagnation detection, divergence detection.

---

## 4. run_benchmark Pattern

Run all configs across all problems at all dimensions with progress reporting.

```julia
function run_benchmark(configs, problems_by_dim;
                       maxiter=5000, show_progress=true, timeout=0.0)
    total = sum(length(configs) * length(probs) for (_, probs) in problems_by_dim)
    results = BenchmarkResult[]
    sizehint!(results, total)

    p = show_progress ? Progress(total; desc="Benchmarking: ", showspeed=true) : nothing

    for (n, probs) in problems_by_dim
        for prob in probs
            for config in configs
                result = run_single(config, prob; maxiter=maxiter, timeout=timeout)
                push!(results, result)

                if show_progress
                    # Rich progress display
                    next!(p; showvalues=[
                        (:dimension, "n=$n"),
                        (:config, config.name),
                        (:problem, get_name(prob)),
                        (:last_result, result.converged ? "$(result.iterations)it" : "NC"),
                    ])
                end
            end
        end
    end

    # Convert to DataFrame
    DataFrame(
        config     = [r.config_name for r in results],
        problem    = [r.problem_name for r in results],
        dimension  = [r.dimension for r in results],
        iterations = [r.iterations for r in results],
        converged  = [r.converged for r in results],
        residual   = [r.final_residual for r in results],
        fw_iters   = [r.fw_iterations for r in results],
        cpu_time   = [r.cpu_time for r in results],
        f_evals    = [r.f_evals for r in results],
        error      = [r.error for r in results],
        error_msg  = [r.error_msg for r in results],
    )
end
```

---

## 5. Starting Points Expansion

Generate diverse starting points and expand the problem list.

```julia
function starting_points(n; k=5, seed=42)
    rng = Random.Xoshiro(seed)
    points = Vector{Float64}[]

    # Deterministic starting points
    push!(points, fill(0.5, n))           # sp1: constant
    push!(points, [1/i for i in 1:n])     # sp2: harmonic
    push!(points, [i/n for i in 1:n])     # sp3: linear

    # Random starting points
    for _ in 1:(k - 3)
        push!(points, rand(rng, n))
    end

    return points
end

function expand_starting_points(problems; k=5, seed=42)
    expanded = Problem[]
    for prob in problems
        n = get_dimension(prob)
        C = get_C(prob)
        sps = starting_points(n; k=k, seed=seed)
        for (i, sp) in enumerate(sps)
            x0 = project_feasible(C, sp)
            push!(expanded, with_x0(prob, x0, "sp$(i)"))
        end
    end
    return expanded
end
```

---

## 6. Dolan-More Performance Profile Matrices

Pivot benchmark results into matrices for performance profiles.

```julia
function build_profile_matrices(df, configs; maxiter=10000)
    df = copy(df)
    df.problem_id = df.problem .* "_n" .* string.(df.dimension)

    problem_ids = unique(df.problem_id)
    config_names = [c.name for c in configs]
    n_problems = length(problem_ids)
    n_configs = length(config_names)

    # Initialize with penalty values
    T_iters = fill(Float64(maxiter), n_problems, n_configs)
    T_feval = fill(Float64(maxiter), n_problems, n_configs)
    max_cpu = maximum(df[df.converged .& .!df.error, :cpu_time]; init=1.0)
    T_cpu   = fill(2.0 * max_cpu, n_problems, n_configs)

    # Fill in converged values
    for (j, cname) in enumerate(config_names)
        for (i, pid) in enumerate(problem_ids)
            rows = filter(r -> r.config == cname && r.problem_id == pid, df)
            if nrow(rows) == 1 && rows.converged[1] && !rows.error[1]
                T_iters[i, j] = Float64(rows.iterations[1])
                T_feval[i, j] = Float64(rows.f_evals[1])
                T_cpu[i, j]   = Float64(rows.cpu_time[1])
            end
        end
    end

    return (T_iters, T_feval, T_cpu, problem_ids, config_names)
end
```

---

## 7. Head-to-Head Comparison Tables

Geometric mean ratios for pairwise comparisons.

```julia
function head_to_head(df, your_algo, other_algo; metrics=[:iterations, :f_evals, :cpu_time])
    # Filter to problems where both converged
    your_df = filter(r -> r.config == your_algo && r.converged && !r.error, df)
    other_df = filter(r -> r.config == other_algo && r.converged && !r.error, df)

    common = innerjoin(
        select(your_df, :problem, :dimension, metrics...),
        select(other_df, :problem, :dimension, metrics...),
        on=[:problem, :dimension],
        renamecols="_yours" => "_other"
    )

    ratios = Dict{Symbol, Float64}()
    for m in metrics
        yours_col = Symbol("$(m)_yours")
        other_col = Symbol("$(m)_other")
        vals = common[!, yours_col] ./ common[!, other_col]
        ratios[m] = exp(mean(log.(vals)))  # geometric mean ratio
    end

    return ratios
end
```

---

## 8. Shifted Geometric Mean (SGM) for Ranking

Standard approach for aggregating iteration/F-eval counts across problems.

```julia
function shifted_geometric_mean(values; shift=1.0)
    shifted = values .+ shift
    exp(mean(log.(shifted))) - shift
end

# For ranking: compute SGM of F-evaluation ratios
function rank_configs(df; feval_penalty=50000)
    df_conv = filter(r -> r.converged && !r.error, df)

    best_fevals = combine(
        groupby(df_conv, [:problem, :dimension]),
        :f_evals => minimum => :best,
    )

    df_scored = leftjoin(df, best_fevals, on=[:problem, :dimension])

    df_scored.ratio = map(eachrow(df_scored)) do row
        if row.error || !row.converged
            Float64(feval_penalty) / max(coalesce(row.best, 1.0), 1.0)
        else
            Float64(row.f_evals) / max(coalesce(row.best, 1.0), 1.0)
        end
    end

    rankings = combine(
        groupby(df_scored, :config),
        :ratio => (r -> shifted_geometric_mean(collect(r))) => :sgm_ratio,
        :converged => mean => :conv_rate,
        :iterations => (x -> mean(Float64.(x[x.>=0]))) => :mean_iters,
    )

    sort!(rankings, :sgm_ratio)
end
```

---

## 9. Problem Collection Helpers

```julia
# Filter out problems where x0 is not feasible
function safe_all_problems(n)
    probs = Problem[]
    for prob in all_problems(n)
        try
            x0 = get_x0(prob)
            C = get_C(prob)
            if x0 ∈ C
                push!(probs, prob)
            end
        catch
        end
    end
    return probs
end
```

---

## 10. Latin Hypercube Sampling

```julia
function latin_hypercube(N, ranges; seed=42)
    rng = Random.Xoshiro(seed)
    d = length(ranges)
    samples = zeros(N, d)

    for j in 1:d
        lo, hi = ranges[j]
        perm = randperm(rng, N)
        for i in 1:N
            u = (perm[i] - 1 + rand(rng)) / N
            samples[i, j] = lo + u * (hi - lo)
        end
    end

    return samples
end
```
