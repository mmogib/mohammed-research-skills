---
name: optimization-research-workflow
description: End-to-end workflow for optimization algorithm research projects.
  Guides through theory, Julia implementation, parameter tuning, benchmarking,
  and paper writing. Use when starting a new optimization research project or
  when needing guidance on the next phase.
invocation: user
---

# Optimization Research Workflow

End-to-end workflow for developing, benchmarking, and publishing optimization algorithms in Julia. Extracted from the MISTDFPM project's battle-tested process.

## Quick Start

1. **Copy the project template:**
   ```
   cp -r "D:\Dropbox\Research\Templates\optimization-research-template" "D:\Dropbox\Research\Projects\YOUR_PROJECT"
   ```

2. **Customize `CLAUDE.md`** — fill in algorithm name, problem class, constraint types.

3. **Customize `jcode/CLAUDE.md`** — fill in algorithm parameters, presets, step descriptions.

4. **Follow the phases below** in order. Each phase has clear decision rules for when to proceed.

## Project Structure

```
your-project/
├── CLAUDE.md              # Project-level context (algorithm, status, rules)
├── jcode/
│   ├── CLAUDE.md          # Implementation-level context (params, scripts, usage)
│   ├── Project.toml       # Julia project dependencies
│   ├── src/               # Source code
│   │   ├── includes.jl    # Entry point
│   │   ├── deps.jl        # Package imports (ALL shared deps here)
│   │   ├── algorithm.jl   # Main algorithm (struct + iterator + solve)
│   │   ├── direction.jl   # Direction computation
│   │   ├── linesearch.jl  # Line search
│   │   ├── projection.jl  # Projection methods
│   │   ├── problems.jl    # Test problem definitions
│   │   └── benchmark.jl   # Multi-solver benchmarking infrastructure
│   └── scripts/           # Experiment scripts (ARGS dispatch pattern)
├── paper/                 # LaTeX manuscript
├── refs/                  # Reference papers (PDFs)
└── notes/                 # Plans, session findings, working documents
```

## Workflow Phases

| # | Phase | Gate to proceed | Typical duration |
|---|-------|----------------|-----------------|
| 0 | Project Setup | Template instantiated, CLAUDE.md customized | 1 session |
| 1 | Theory Review | Algorithm understood, proofs checked | 1-2 sessions |
| 2 | Core Implementation | Algorithm struct + iterator + solve working | 2-3 sessions |
| 3 | Reference Algorithms | Comparison methods implemented | 1-2 sessions |
| 4 | Smoke Tests | All algorithms pass on small problems | 1 session |
| 5 | Sensitivity Analysis | Critical parameters identified | 1-2 sessions |
| 6 | Parameter Search | Tuned defaults found via LHS | 1-2 sessions |
| 7 | Large-Scale Experiments | Multi-dim x multi-problem x multi-start results | 1-2 sessions |
| 8 | Application Experiments | Domain-specific results (CS, traffic, etc.) | 1-3 sessions |
| 9 | Paper Writing | Results tables, profiles, discussion | 2-3 sessions |
| 10 | Literature Review | Introduction, related work | 1-2 sessions |
| 11 | Polish & Submit | Notation, proofs, abstract, final check | 1-2 sessions |

**See `references/workflow-phases.md` for detailed phase descriptions, decision rules, and common pitfalls.**

## Key Patterns

- **ARGS dispatch** — All scripts support `julia --project=. scripts/XX_name.jl part1 part2` for modular execution. See `references/script-patterns.md`.
- **CSV accumulation** — Benchmark tiers save independently; re-running a tier replaces only its rows. See `references/benchmark-patterns.md`.
- **Batch checkpointing** — Parameter search saves after every N configs; auto-resumes on restart.
- **SolverConfig pattern** — Uniform `(name, kwargs, constructor)` interface for multi-solver benchmarks.

## Rules

1. **DO NOT compile LaTeX.** The user compiles.
2. **DO NOT run scripts automatically.** The user runs scripts unless they explicitly ask.
3. **All shared deps in `deps.jl`.** Never add `using` in other `src/` files.
4. **Notes workflow.** Plans go to `notes/plan_*.md`. Session findings go to `notes/`.
5. **Status tracking.** Keep `CLAUDE.md` current with completed items, findings, next steps.

## Reference Files

- `references/workflow-phases.md` — Detailed 12-phase guide with decision rules
- `references/script-patterns.md` — Reusable Julia script patterns
- `references/benchmark-patterns.md` — Benchmarking infrastructure patterns
- `references/claude-md-guide.md` — How to write effective CLAUDE.md files
- `references/template-usage.md` — How to instantiate and customize the template
