# Infrastructure Patterns — io_utils.jl and utils.jl

Canonical code for the two shared utility files that scripts depend on. When creating these files, adapt to the project's architecture style.

---

## io_utils.jl — TeeIO Logging

This file provides dual-output logging: everything printed via `tee` goes to both the console and a timestamped log file.

### Canonical Code

```julia
# ============================================================================
# I/O Utilities: TeeIO, logging
# ============================================================================

using Dates

# --- TeeIO: simultaneous write to two IO streams ---

"""
    TeeIO(a::IO, b::IO)

IO wrapper that writes to both `a` and `b` simultaneously.
Used for console + logfile output without pipe-based redirection.
"""
struct TeeIO <: IO
    a::IO   # primary (console)
    b::IO   # secondary (log file)
end

function Base.unsafe_write(t::TeeIO, p::Ptr{UInt8}, n::UInt)
    Base.unsafe_write(t.a, p, n)
    Base.unsafe_write(t.b, p, n)
    return n
end

Base.flush(t::TeeIO) = (flush(t.a); flush(t.b))
Base.isopen(t::TeeIO) = isopen(t.a) && isopen(t.b)

# --- Logging ---

"""
    setup_logging(script_name::String; logdir=nothing)

Open a log file and return a `TeeIO` that writes to both console and log.
Returns `(logpath, tee, logfile)`.

Call `teardown_logging(tee, logpath)` when done.
"""
function setup_logging(script_name::String; logdir::Union{Nothing,String}=nothing)
    if logdir === nothing
        logdir = joinpath(@__DIR__, "..", "results", "logs")
    end
    mkpath(logdir)

    timestamp = Dates.format(Dates.now(), "yyyymmdd_HHMMss")
    logpath = joinpath(logdir, "log_$(script_name)_$(timestamp).txt")
    logfile = open(logpath, "w")

    tee = TeeIO(stdout, logfile)

    println(tee, "Log file: $logpath")
    println(tee, "Timestamp: $timestamp")
    println(tee)

    return (logpath, tee, logfile)
end

"""
    teardown_logging(tee::TeeIO, logpath::String)

Flush and close the log file.
"""
function teardown_logging(tee::TeeIO, logpath::String)
    flush(tee)
    close(tee.b)
    println("Log saved to: $logpath")
end
```

### Integration Notes

**logdir path**: The `setup_logging` function above uses a relative `@__DIR__` path. This works when io_utils.jl is in `src/` and scripts are in `scripts/`. Adjust the default logdir path if the project structure differs.

**Style A (Module Package)**:
- The module version should use `pkgdir(@__MODULE__)` instead of `@__DIR__` for the default logdir:
  ```julia
  logdir = joinpath(pkgdir(@__MODULE__), "results", "logs")
  ```
- Add `include("io_utils.jl")` to the main module file
- Add exports: `TeeIO, setup_logging, teardown_logging`

**Style B (Flat Include)**:
- Add `include("io_utils.jl")` to `includes.jl` (after `deps.jl`, before algorithm files)
- No exports needed (everything is in global namespace)

---

## utils.jl — Shared Helpers

A collection of project-relevant utility functions. Not every project needs all of these — include only what's relevant.

### Derivative Checking

Required for verification scripts (`s10_`). Compares analytical Jacobian against central finite differences.

```julia
"""
    check_derivatives(F, JF, x; ind=nothing, eps1=1e-5, eps2=1e-5)

Check analytical gradients against central finite differences.

# Arguments
- `F`: objective function F: Rⁿ → Rᵐ (returns vector)
- `JF`: Jacobian function JF: Rⁿ → Rᵐˣⁿ
- `x`: point at which to check
- `ind`: objective index to check (nothing = check all)
- `eps1`, `eps2`: step sizes for finite differences

# Returns
- `max_error`: maximum absolute error across all checked components
- `errors`: dictionary mapping (objective_index, variable_index) → error
"""
function check_derivatives(F::Function, JF::Function, x::AbstractVector;
                           ind::Union{Nothing,Int}=nothing,
                           eps1::Float64=1e-5, eps2::Float64=1e-5)
    n = length(x)
    J_analytic = JF(x)
    m = size(J_analytic, 1)

    indices = ind === nothing ? (1:m) : [ind]
    max_error = 0.0
    errors = Dict{Tuple{Int,Int}, Float64}()

    x_work = copy(x)

    for i in indices
        for j in 1:n
            tmp = x_work[j]

            # First step size
            step1 = eps1 * max(abs(tmp), 1.0)
            x_work[j] = tmp + step1
            fp1 = F(x_work)[i]
            x_work[j] = tmp - step1
            fm1 = F(x_work)[i]
            gdiff1 = (fp1 - fm1) / (2.0 * step1)

            # Second step size
            step2 = eps2 * max(abs(tmp), 1e-3)
            x_work[j] = tmp + step2
            fp2 = F(x_work)[i]
            x_work[j] = tmp - step2
            fm2 = F(x_work)[i]
            gdiff2 = (fp2 - fm2) / (2.0 * step2)

            x_work[j] = tmp

            err = min(abs(J_analytic[i,j] - gdiff1), abs(J_analytic[i,j] - gdiff2))
            errors[(i, j)] = err
            max_error = max(max_error, err)
        end
    end

    return max_error, errors
end
```

### Elapsed Time Formatting

Simple helper used across multiple scripts.

```julia
"""
    format_elapsed(seconds) -> String

Format elapsed time as human-readable string (e.g., "42s", "3.2m", "1.5h").
"""
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

### Scaling Helpers

Gradient-based scaling for objectives with different magnitudes.

```julia
"""
    scale_factors(JF_x, m)

Compute scaling factors: sF[i] = 1 / max_j |∇F_i(x)[j]|.
If the gradient is zero, sF[i] = 1.
"""
function scale_factors(JF_x::AbstractMatrix, m::Int)
    sF = ones(m)
    for i in 1:m
        max_grad = maximum(abs.(JF_x[i, :]))
        if max_grad > 0
            sF[i] = 1.0 / max_grad
        end
    end
    return sF
end

"""
    apply_scaling(F_orig, JF_orig, sF)

Return scaled versions of F and JF: F̃_i(x) = sF[i] * F_i(x).
"""
function apply_scaling(F_orig::Function, JF_orig::Function, sF::AbstractVector)
    F_scaled(x) = sF .* F_orig(x)
    JF_scaled(x) = Diagonal(sF) * JF_orig(x)
    return F_scaled, JF_scaled
end
```

### Nonsmooth Subgradient Helpers

Only needed for projects with nonsmooth objectives.

```julia
abs_subgrad(x::Real) = x > 0 ? 1.0 : (x < 0 ? -1.0 : 0.0)

function l1_subgrad(x::AbstractVector, a::AbstractVector)
    return [abs_subgrad(x[i] - a[i]) for i in eachindex(x)]
end

function max_subgrad(fvals::AbstractVector, grads::AbstractVector)
    _, idx = findmax(fvals)
    return grads[idx]
end

function min_subgrad(fvals::AbstractVector, grads::AbstractVector)
    _, idx = findmin(fvals)
    return grads[idx]
end
```

### Integration Notes

**Style A (Module Package)**:
- Add `include("utils.jl")` to the main module file
- Export the functions that scripts need (e.g., `check_derivatives, format_elapsed, scale_factors, apply_scaling`)

**Style B (Flat Include)**:
- Add `include("utils.jl")` to `includes.jl` (after `deps.jl`, before algorithm files)
- No exports needed

### What to Include Per Project

| Helper | Include when... |
|--------|----------------|
| `check_derivatives` | Project has analytical gradients/Jacobians to verify |
| `format_elapsed` | Any script with timing (most scripts) |
| `scale_factors` / `apply_scaling` | Multi-objective with different magnitude objectives |
| Subgradient helpers | Nonsmooth objectives (max, abs, L1 norm) |
| Shifted geometric mean | OAT or LHS parameter search scripts |

Don't include helpers that the project doesn't need. Keep utils.jl lean.
