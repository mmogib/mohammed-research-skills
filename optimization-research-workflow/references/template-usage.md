# Template Usage Guide

How to instantiate and customize the optimization research project template.

---

## Step 1: Copy the Template

```bash
cp -r "D:\Dropbox\Research\Templates\optimization-research-template" \
      "D:\Dropbox\Research\Projects\YOUR_PROJECT_NAME"
cd "D:\Dropbox\Research\Projects\YOUR_PROJECT_NAME"
```

## Step 2: Initialize Git

```bash
git init
git add -A
git commit -m "Initial project from optimization-research-template"
```

## Step 3: Customize CLAUDE.md (Project Level)

Edit the root `CLAUDE.md`:

1. **Replace `ALGORITHM_NAME`** throughout with your algorithm's abbreviation (e.g., MISTTDFPM, IHSCG, ADMM).
2. **Update Overview** with your problem class and algorithm description.
3. **Update Rules** section:
   - Keep rules 1-3 (LaTeX, code execution, notes workflow) as-is.
   - Add any project-specific constraints.
4. **Clear Current Status** — start fresh with "Project initialized from template."
5. **Update Pointers** if you rename files or add new source files.

## Step 4: Customize jcode/CLAUDE.md

Edit `jcode/CLAUDE.md`:

1. **Replace `MyAlgorithm`** throughout with your algorithm name.
2. **Fill in the Algorithm Summary table** with your algorithm's steps.
3. **Fill in the Parameters table** with your algorithm's parameters, defaults, and constraints.
4. **Update File Structure** as you add reference algorithms.
5. **Update Usage examples** with your algorithm's constructor and options.

## Step 5: Set Up Julia Project

```bash
cd jcode
julia --project=. -e '
    import Pkg
    # Add your core dependencies
    Pkg.add(["LazySets", "DataFrames", "CSV", "ProgressMeter"])
    # Add optional dependencies
    Pkg.add(["Plots", "BenchmarkProfiles", "Colors"])
    Pkg.instantiate()
'
```

Edit `Project.toml` as needed for additional dependencies (e.g., `Wavelets`, `Images`).

## Step 6: Customize Source Files

### `src/deps.jl`
Add your package imports. The template includes the standard set; add domain-specific packages here.

### `src/algorithm.jl`
This is the main file to customize:
1. Define your algorithm struct with all parameters.
2. Implement constructor with parameter validation.
3. Implement `Base.iterate(m)` for initialization.
4. Implement `Base.iterate(m, state)` for iteration.
5. The `solve()` function can usually be kept as-is.

### `src/direction.jl`
Implement your direction computation (gradient, conjugate gradient, spectral, three-term, etc.).

### `src/linesearch.jl`
Implement your line search (Armijo, Wolfe, nonmonotone, etc.). The template provides an Armijo skeleton.

### `src/projection.jl`
Implement projection methods for your constraint types. The template provides Ball2 and Hyperrectangle.

### `src/problems.jl`
Define your test problems:
1. Nonlinear functions F: R^n -> R^n
2. Constraint set factories
3. Problem constructors combining F + C + x0
4. `all_problems(n)` returning all test problems at dimension n
5. Starting point infrastructure (copy from template)

### `src/benchmark.jl`
**Keep mostly as-is.** This is the most reusable piece. Only change:
- The default constructor in `SolverConfig` (line with `SolverConfig(name, kwargs) = ...`).
- Add custom metrics to `BenchmarkResult` if needed.

## Step 7: Customize Scripts

### `scripts/01_smoke_test.jl`
1. Update the `ALGS` array with your algorithms.
2. Add/remove test problems to match `problems.jl`.
3. Add tests for your parameter variants.

### `scripts/10_sensitivity.jl`
1. Update `PARAM_SWEEPS` with your algorithm's parameters and sweep ranges.
2. Update `DIMENSIONS` for your problem scale.

### `scripts/20_param_search.jl`
1. Update `SEARCH_PARAMS` with your parameter space.
2. Update `FIXED_PARAMS` for parameters not being searched.
3. Update `TIERS` dimensions for your problem scale.
4. Update `validate_search_params()` with your convergence constraints.

### `scripts/50_experiment1.jl`
1. Update `BASE_CONFIGS` with your solvers.
2. Update `DIMS_*` for your experiment dimensions.
3. Update style constants for your solver count.

### `scripts/65_application.jl`
Customize entirely for your application (compressed sensing, traffic, etc.).

## Step 8: Add Reference Papers

Copy reference papers (PDFs) to `refs/`. Use descriptive filenames:
```
refs/
├── Smith2024_spectral_cg.pdf
├── Chen2023_inertial_projection.pdf
└── Li2022_three_term_dfp.pdf
```

## Step 9: Verify Setup

```bash
cd jcode
julia --project=. -e 'include("src/includes.jl"); println("Setup OK")'
```

If this prints "Setup OK", the project is ready for Phase 1 (Theory Review).

---

## What to Keep As-Is

These components are designed to be reused without modification:

1. **`benchmark.jl`** — SolverConfig, run_single, run_benchmark, build_profile_matrices
2. **`solve()` function** — iterator-based solve loop with stagnation/divergence/timeout
3. **ARGS dispatch pattern** — in all scripts
4. **CSV accumulation pattern** — tier-based merge
5. **Batch checkpointing** — in parameter search
6. **Logging infrastructure** — setup_logging / teardown_logging
7. **LaTeX table generation** — generate_latex_table
8. **Performance profile styling** — styled_profile

## What Must Be Customized

1. **Algorithm struct and iterator** — unique to each algorithm
2. **Direction and line search** — algorithm-specific
3. **Test problems** — may overlap but new problems are often needed
4. **Parameter constraints** — derived from convergence theory
5. **Parameter search space** — algorithm-specific ranges
6. **Paper content** — always unique
