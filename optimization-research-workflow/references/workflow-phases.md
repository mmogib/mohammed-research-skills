# Workflow Phases — Detailed Guide

Each phase includes objectives, inputs/prerequisites, outputs, decision rules for proceeding, and common pitfalls.

---

## Phase 0: Project Setup

**Objective:** Instantiate the project template and customize for your specific algorithm.

**Inputs:** Algorithm name, paper draft or idea, reference papers.

**Steps:**
1. Copy `optimization-research-template/` to your project location.
2. Edit `CLAUDE.md` — fill in algorithm name, problem class (e.g., "nonlinear equations with convex constraints"), constraint types.
3. Edit `jcode/CLAUDE.md` — fill in algorithm steps, parameter descriptions, preset placeholders.
4. Add reference papers to `refs/`.
5. Run `julia --project=jcode/ -e 'import Pkg; Pkg.instantiate()'` to set up dependencies.

**Outputs:** Working project skeleton with customized CLAUDE.md files.

**Gate:** Can open the project in Claude Code and Claude understands the algorithm from CLAUDE.md.

**Pitfalls:**
- Don't over-customize the template before the algorithm is implemented; you'll revise CLAUDE.md many times.
- Make sure `deps.jl` loads without error before proceeding.

---

## Phase 1: Theory Review

**Objective:** Thoroughly understand the algorithm, its convergence theory, and assumptions.

**Inputs:** Reference papers in `refs/`.

**Steps:**
1. Read the paper carefully. Use `document-skills:pdf` to extract key sections.
2. Identify the algorithm steps (initialization, direction, line search, projection, update).
3. Map each step to required functions (e.g., Step 3 → `compute_direction()`).
4. List all parameters with their constraints (e.g., ρ ∈ (0,1), θ̄ < 1/2).
5. Check convergence proof assumptions — which are used in practice vs. theoretical artifacts?
6. Identify what makes this algorithm novel vs. references.

**Outputs:** Updated `jcode/CLAUDE.md` with algorithm summary table, parameter table, convergence constraints.

**Gate:** Can explain the algorithm step-by-step and list all parameter constraints from memory.

**Pitfalls:**
- Skipping the convergence proof review. Parameter constraints often come from convergence conditions (e.g., γ < 2/(2θ̄+1)).
- Not identifying coupled constraints between parameters early.

---

## Phase 2: Core Implementation

**Objective:** Implement the main algorithm as a Julia struct with iterator protocol.

**Inputs:** Algorithm description from Phase 1.

**Steps:**
1. Define the algorithm struct with all parameters and counters.
2. Implement the constructor with parameter validation (assertions for convergence constraints).
3. Implement `Base.iterate(m)` — first iteration (initialization).
4. Implement `Base.iterate(m, state)` — subsequent iterations.
5. Implement `solve(m; maxiter, verbose, ...)` — convenience wrapper.
6. Add preset system: `PRESETS = Dict(:default => (;), :paper => (...), ...)`.
7. Test on a trivial problem: `F(x) = exp.(x) .- 1`, `C = Ball2(zeros(n), 1.0)`.

**Key design decisions:**
- State tuple: `(x_prev, x, w_prev, d_prev, F_w_prev, k, is_first, p_prev)` — include everything needed for the next iteration.
- F-evaluation counting: wrap F in a counting closure in the constructor.
- Line search tracking: `LSTracker` records per-iteration backtracking counts and detects consecutive failures.

**Outputs:** Working `algorithm.jl` that solves trivial problems.

**Gate:** `solve(MyAlgorithm(F, C); maxiter=100)` converges on ExponentialI with Ball2.

**Pitfalls:**
- Forgetting to reset counters in `iterate(m)` (first call).
- Not handling the first iteration specially (no inertia, d₀ = -F(x₀)).
- Mutable state in the iterator — use the state tuple pattern, not struct fields.

---

## Phase 3: Reference Algorithms

**Objective:** Implement comparison algorithms from the literature.

**Inputs:** Reference papers, understanding of how they differ from your algorithm.

**Steps:**
1. Identify 2-4 reference algorithms (typically: the algorithm your method improves upon, plus 1-2 from related recent papers).
2. Implement each using the same `struct + iterator + solve` pattern.
3. Ensure the same `solve()` interface: `x, k, converged = solve(solver; maxiter, verbose, ...)`.
4. All share the same counters and tracking infrastructure.

**Design:** Same `solve()` function works for all algorithms because they all implement the Julia iterator protocol. Differences are in the struct and `iterate()` methods.

**Outputs:** Reference algorithm files (e.g., `IHZIPM.jl`, `ISTTDFPM.jl`).

**Gate:** All reference algorithms solve ExponentialI-Ball2. All use the same `SolverConfig` pattern.

**Pitfalls:**
- Implementing too many references — 3-5 is usually sufficient.
- Not matching paper defaults exactly (check parameter names and default values carefully).

---

## Phase 4: Smoke Tests

**Objective:** Verify all algorithms work correctly on a diverse set of small problems.

**Inputs:** `problems.jl` with test problems, all algorithm implementations.

**Steps:**
1. Run `01_smoke_test.jl` — tests trivial problems, ball problems, box problems, higher dimensions, all parameter variants.
2. Fix any errors or assertion failures.
3. Add tests for special cases: all p_choice options, all ls_type variants, all direction types, all constraint types.
4. Verify `SolverConfig` pattern works with all algorithms.

**Outputs:** `01_smoke_test.jl` passes all tests (target: 95%+ pass rate).

**Gate:** >95% of smoke tests pass. Any failures are understood (e.g., known-hard problems that all solvers fail on).

**Pitfalls:**
- Not testing all parameter combinations — smoke tests should cover the full combinatorial space.
- Ignoring errors from reference algorithms — they may indicate bugs in shared infrastructure.

---

## Phase 5: Sensitivity Analysis

**Objective:** Understand which parameters matter most and identify good ranges.

**Inputs:** Working algorithm + reference algorithms, test problems.

**Steps:**
1. Design OAT (One-At-a-Time) sweeps: for each parameter, vary it across a range while holding others at defaults.
2. Include both continuous parameters (η, ρ, κ, λ, α_min, ...) and categorical (p_choice, ls_type, dir_type).
3. Run across 2-3 small dimensions (e.g., [10, 100, 1000]).
4. Measure: convergence rate, mean iterations, mean F-evaluations, mean CPU time.
5. Rank parameters by importance (iteration count range across sweep values).
6. Identify coupled parameters and run joint sweeps if needed (e.g., γ-θ̄ grid).

**Outputs:** `results/sensitivity/` with CSV files and plots. Importance ranking.

**Gate:** Can identify top 3-5 most impactful parameters. No parameter causes catastrophic failure at its default value.

**Pitfalls:**
- Using too few sweep values — 5-7 values per parameter is a good balance.
- Not running at multiple dimensions — a parameter that's good at n=10 may be bad at n=10000.
- Ignoring categorical parameters — p_choice and ls_type can have 50%+ impact.

---

## Phase 6: Parameter Search

**Objective:** Find optimal default parameters using Latin Hypercube Sampling.

**Inputs:** Sensitivity analysis results (which parameters to search, good ranges).

**Steps:**
1. Define search space: continuous ranges informed by sensitivity analysis, categorical combinations.
2. Generate LHS samples (50+ per tier) with deterministic seeds for reproducibility.
3. Run across multiple scale tiers (e.g., Small [1k-10k], Mid [20k-50k], Large [75k-120k]).
4. Rank by shifted geometric mean (SGM) of F-evaluation ratios.
5. Cross-compare top configs from each tier at representative dimensions.
6. Select universal winner or scale-dependent presets.
7. Update constructor defaults and PRESETS dictionary.

**Key patterns:**
- Batch checkpointing: save after every N configs, auto-resume on restart.
- Deterministic seeds: same samples every run for reproducibility.
- Validation: reject parameter combinations that violate convergence constraints before running.

**Outputs:** Tuned default parameters, optional scale-dependent presets. Updated `MISTTDFPM_PRESETS`.

**Gate:** Tuned defaults outperform paper defaults on SGM metric. New defaults pass smoke tests.

**Pitfalls:**
- Not validating parameter combinations — many LHS samples will violate coupled constraints.
- Searching too narrow a range — use sensitivity analysis to set bounds.
- Not including baselines in the search — always compare against paper defaults.

---

## Phase 7: Large-Scale Experiments (Paper Experiment 1)

**Objective:** Comprehensive benchmarking for the paper's main numerical experiment.

**Inputs:** Tuned algorithm + reference algorithms, full problem set with starting points.

**Steps:**
1. Define dimensions: 9 dimensions spanning 1k to 120k.
2. Define problems: all standard test problems (16+).
3. Define starting points: 10 diverse starting points per problem.
4. Define solvers: your algorithm + 4-5 references.
5. Run benchmark in tiers (small/mid/large) for manageability.
6. Generate: summary table, per-dimension tables, performance profiles (iterations, F-evals, CPU), head-to-head comparisons.
7. Write results into paper Section 4.

**Key metrics:**
- Convergence rate across all problems
- Mean iterations (lower is better)
- Mean F-evaluations (lower is better)
- Geometric mean ratios in head-to-head comparisons (<1 = your algorithm wins)
- Dolan-Moré performance profiles

**Outputs:** `results/experiment1/` with raw CSV, profile matrices, plots, LaTeX tables.

**Gate:** Your algorithm shows clear advantages on at least one major metric (iterations or F-evals). Results are consistent across dimensions.

**Pitfalls:**
- Running too few starting points — 5-10 provides robust statistics.
- Not using CSV accumulation — if the run crashes, you lose everything. The tier-based approach preserves completed tiers.
- Forgetting to set timeout — some problems diverge and waste hours.

---

## Phase 8: Application Experiments (Paper Experiment 2+)

**Objective:** Demonstrate the algorithm on domain-specific applications.

**Inputs:** Application problem formulations (e.g., compressed sensing, traffic assignment).

**Steps:**
1. Formulate the application as F(x) = 0 with x ∈ C. Prove monotonicity.
2. Implement problem constructors in `problems.jl`.
3. Add smoke test for the application.
4. (Optional) Run application-specific sensitivity analysis and parameter search.
5. Run benchmark across problem sizes.
6. Generate application-specific metrics (e.g., PSNR for image restoration, gap for traffic).

**Common applications for projection methods:**
- Compressed sensing (GPSR → NCP → Fischer-Burmeister)
- Image restoration (blur + noise → deconvolution)
- Traffic assignment (user equilibrium → NCP)
- Variational inequalities → nonlinear equations

**Outputs:** Application results, domain-specific plots, filled paper tables.

**Gate:** Algorithm converges on application problems. Results are competitive with or better than references.

**Pitfalls:**
- Measurement matrix choice for compressed sensing — random Gaussian often fails; structured matrices (scrambled Hadamard) are more reliable.
- Application problems may need different ε tolerance than standard problems.
- Small-scale applications (n < 100) don't showcase large-scale advantages.

---

## Phase 9: Paper Writing — Results

**Objective:** Write the numerical experiments section with results tables, figures, and discussion.

**Inputs:** All experiment results, performance profiles, summary statistics.

**Steps:**
1. Use `math-research-writer` skill for structured paper writing.
2. Write problem table (Table 1): problem name, F description, constraint, source citation.
3. Write summary table: per-solver convergence rate, mean iterations, mean F-evals across all dimensions.
4. Write head-to-head comparison table: geometric mean ratios for each metric.
5. Include performance profile figures.
6. Write discussion: what your algorithm does best, where trade-offs exist (e.g., CPU vs iterations).
7. Write application results section.

**Outputs:** Complete Section 4 (Numerical Experiments) in `paper/main.tex`.

**Gate:** All tables and figures are filled in. Discussion accurately reflects the data.

---

## Phase 10: Literature Review & Introduction

**Objective:** Write introduction, related work, and position the contribution.

**Steps:**
1. Use reference papers in `refs/` to write related work.
2. Identify the gap your algorithm fills.
3. Write contribution list.
4. Write introduction following the "problem → context → gap → contribution → outline" structure.

**Outputs:** Sections 1-2 of paper.

---

## Phase 11: Polish & Submit

**Objective:** Final review and preparation for submission.

**Steps:**
1. Notation consistency check: same symbols throughout paper and code.
2. Proof review: all lemmas and theorems referenced correctly.
3. Abstract update: reflects actual results, not aspirational claims.
4. Use `title-abstract` skill for title and abstract refinement.
5. Check bibliography completeness.
6. Final read-through for clarity and flow.

**Outputs:** Submission-ready paper.

**Gate:** Paper compiles cleanly. All results are reproducible from the code.
