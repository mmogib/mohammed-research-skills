# Dependency Guide — Feature → Package Mapping

Maps script features to the Julia packages they require, and explains how to add dependencies for each architecture style.

---

## Feature → Package Mapping

| Feature | Required Packages | Notes |
|---------|------------------|-------|
| TeeIO logging | `Dates` (stdlib) | No external package needed |
| ARGS parsing | — | Pure Julia, no packages |
| CSV I/O (manual) | `Printf` (stdlib) | Manual @printf to file; no CSV.jl needed |
| CSV I/O (DataFrames) | `CSV`, `DataFrames` | Only if aggregation needs DataFrame operations |
| ProgressMeter | `ProgressMeter` | Progress bars for long loops |
| Formatted output | `Printf` (stdlib) | Always available |
| Statistics (median, etc.) | `Statistics` (stdlib) | Usually already available |
| Random starts | `Random` (stdlib) | For RNG seeding |
| Scaling helpers | `LinearAlgebra` (stdlib) | For `Diagonal`, `norm` |
| Figure generation | `Plots`, `LaTeXStrings` | Add backend: `pgfplotsx()` or `gr()` |
| Benchmark profiles | `BenchmarkProfiles` | Dolan-Moré performance profiles |
| LaTeX table output | `Printf` (stdlib) | Manual formatting, no extra package |

### Standard library packages (always available, no installation needed)
`LinearAlgebra`, `Printf`, `Random`, `Statistics`, `Dates`, `SparseArrays`, `Test`

### Common external packages by script type

**Verification (`s10_`):**
- No extra packages beyond what the module/source already uses

**OAT / LHS (`s35_`, `s40_`):**
- `Statistics` (stdlib) — for median, mean, quantile

**Multi-start benchmark (`s45_`):**
- `ProgressMeter` — progress bars
- `Statistics` (stdlib) — for aggregation
- `Random` (stdlib) — for RNG seeding

**Ablation (`s50_`):**
- `Statistics` (stdlib) — for aggregation

**Figure generation (`s70_`, `s75_`):**
- `Plots` — plotting library
- `LaTeXStrings` — for L"..." string macros
- Backend choice: `gr()` (default, good PDF) or `pgfplotsx()` (LaTeX-native)

**Application (`s60_`):**
- Same as multi-start benchmark

---

## Adding Dependencies

### Style A — Module Package

Dependencies go in **two places**:

1. **`Project.toml`** — add to `[deps]` section:
   ```toml
   [deps]
   ProgressMeter = "92933f4c-e287-5a05-a399-4b506bd553cd"
   ```
   Then run `julia --project=. -e 'using Pkg; Pkg.instantiate()'` to install.

   Alternatively, install via Pkg REPL:
   ```
   julia --project=.
   ] add ProgressMeter
   ```

2. **Module file** — if the package is used in `src/` code (not just scripts):
   ```julia
   module ModuleName
   using ProgressMeter   # add here
   # ...
   end
   ```

   If the package is **only used in scripts** (not in src/), add `using` directly in the script instead:
   ```julia
   push!(LOAD_PATH, joinpath(@__DIR__, "..", "src"))
   using ModuleName
   using ProgressMeter   # script-only dependency
   ```

### Style B — Flat Include

Dependencies go in **`src/deps.jl`**:

```julia
# deps.jl — All shared dependencies in one place
using LinearAlgebra
using Printf
using Random
using Statistics
using Dates
using ProgressMeter   # add new packages here
```

If a package is **only used by one script** and not by any `src/` code, add it in the script instead:
```julia
include(joinpath(@__DIR__, "..", "src", "includes.jl"))
using Plots              # script-specific
using BenchmarkProfiles   # script-specific
```

### Installation

For both styles, packages must be in `Project.toml` to be importable. If a package is not yet in `Project.toml`:

```bash
cd jcode
julia --project=. -e 'using Pkg; Pkg.add("PackageName")'
```

Or interactively:
```
julia --project=.
] add PackageName
```

---

## Package UUIDs (for manual Project.toml editing)

Common packages and their UUIDs:

```
BenchmarkProfiles = "7f5391cf-bfc8-5e1e-b2c0-61b7876d7127"
CSV = "336ed68f-0bac-5ca0-87d4-7b16caf5d00b"
DataFrames = "a93c6f00-e57d-5684-b7b6-d8193f3e46c0"
LaTeXStrings = "b964fa9f-0449-5b57-a5c2-d3ea65f4040f"
Plots = "91a5bcdd-55d7-5caf-9e0b-520d859cae80"
ProgressMeter = "92933f4c-e287-5a05-a399-4b506bd553cd"
BenchmarkProfiles = "7f5391cf-bfc8-5e1e-b2c0-61b7876d7127"
```

Standard library packages (`LinearAlgebra`, `Printf`, `Random`, `Statistics`, `Dates`, `SparseArrays`, `Test`) do not need UUIDs — they are always available.

---

## Decision: Module-Level vs Script-Level

**Put in module/deps.jl** when:
- Multiple scripts use it
- It's used by `src/` code (algorithm, utils, io_utils)
- It's a core dependency of the project

**Put in script only** when:
- Only one script uses it (e.g., `Plots` only in figure scripts)
- It's heavy and would slow down module load time for all scripts
- It's a visualization or post-processing tool

Rule of thumb: `Plots`, `LaTeXStrings`, `BenchmarkProfiles` go in scripts. Everything else goes in module/deps.jl.
