---
name: jcode-script
description: Create experiment scripts with consistent structure and patterns.
  Guides through script type selection, feature composition, dependency management,
  and infrastructure setup (io_utils.jl, utils.jl). Ensures scripts follow
  established conventions for ARGS parsing, CSV I/O, resume, logging, and more.
invocation: user
---

# /jcode-script — Experiment Script Generator

Create experiment scripts with consistent structure and patterns. Works with both Style A (Module Package) and Style B (Flat Include) Julia projects, and can adapt principles to Python or MATLAB when explicitly requested.

## Workflow

### Phase 1: Context Discovery

Before asking any questions, gather project context:

1. **Read the project's `CLAUDE.md`** (or `jcode/CLAUDE.md`) to determine:
   - Coding architecture: Style A (module) or Style B (flat include)
   - Algorithm name, problem domain, key types
   - Existing dependencies
   - Module name (Style A) or entry point path (Style B)

2. **Scan existing scripts** in `scripts/` (or ask user for location if no standard structure):
   - List existing `s{NN}_*.jl` files to determine next available number
   - Note which features are already in use (resume, summary mode, etc.)

3. **Check existing infrastructure**:
   - Does `io_utils.jl` exist? Does it have TeeIO, setup_logging, teardown_logging?
   - Does `utils.jl` exist? What helpers does it provide?
   - Does `results/` directory exist? What subdirectories?

If the project directory structure is not standard (no `scripts/`, no `src/`), ask the user where to save the script and where the source code lives.

### Phase 2: User Questions

Ask the user three things (use AskUserQuestion tool):

#### Question 1: Script Type

Present script types with suggested `s{NN}_` numbers based on **logical order** in the experiment pipeline. If an existing script already occupies a number, suggest the next available gap.

| Logical Order | Type | Suggested Prefix | Purpose |
|---|---|---|---|
| 1 | Verification | `s10_` | Gradient/derivative checks, sanity tests |
| 2 | Single-run test | `s20_` | Run algorithm on a few problems, verify it works |
| 3 | Comparison run | `s30_` | Run alternative/baseline algorithm for comparison |
| 4 | Parameter screening (OAT) | `s35_` | One-at-a-time sensitivity analysis |
| 5 | Parameter tuning (LHS) | `s40_` | Latin Hypercube or grid search for best params |
| 6 | Multi-start benchmark | `s45_` | Full benchmark: N problems × M starting points |
| 7 | Ablation study | `s50_` | Isolate effect of a specific design choice |
| 8 | Application experiment | `s60_` | Real-world / application problems |
| 9 | Extension experiment | `s65_` | Algorithm extensions (e.g., general cones) |
| 10 | Figure generation | `s70_` | Publication-quality plots from CSV data |
| 11 | Table generation | `s75_` | LaTeX tables from CSV data |
| 12 | Custom | User specifies | Any other script type |

**Numbering rules:**
- Scripts are numbered in increments of 10 (with 5-gaps for insertion)
- If `s10_` exists and user wants another verification script, suggest `s11_` or `s12_`
- If user wants a type between two existing scripts, suggest the midpoint
- Always confirm the final name with the user

#### Question 2: Features

Based on the script type AND the project context, propose a curated feature list. Mark features as recommended (ON) or optional (OFF) based on what makes sense for this specific script type and project.

Available features (see `references/script-patterns.md` for code blocks):

| Feature | What it adds |
|---|---|
| **Header comment block** | Goal, output, usage examples (always included) |
| **ARGS parsing** | `--all`, ID ranges (`1-10`), `--verbose`, custom flags |
| **`--resume` support** | Skip completed rows by reading existing CSV |
| **`--summary` mode** | Two-mode script: solve vs. post-process |
| **TeeIO logging** | Dual console + log file output via setup_logging |
| **CSV I/O (raw)** | Per-solve CSV with immediate flush |
| **CSV I/O (summary)** | Aggregated statistics CSV |
| **ProgressMeter** | Progress bars for long-running loops |
| **Adaptive parameters** | ε/maxiter that depend on problem dimension |
| **Random feasible starts** | Generate random starting points in the feasible set |
| **Per-iteration history** | Record convergence data for later plotting |
| **Try-catch per solve** | Error handling with CSV error rows |
| **Formatted output table** | `@printf` aligned columns to console |
| **Elapsed time tracking** | Per-problem and total elapsed time formatting |

**Default recommendations by script type:**

- **Verification**: ARGS, TeeIO, formatted output. No CSV, no resume.
- **Single-run / Comparison**: ARGS, TeeIO, formatted output. Optional CSV.
- **OAT screening**: ARGS, TeeIO, CSV raw + summary, try-catch, formatted output, elapsed time.
- **LHS tuning**: ARGS, TeeIO, CSV raw + summary, try-catch, formatted output, elapsed time.
- **Multi-start benchmark**: ALL features recommended (ARGS, resume, summary, TeeIO, CSV raw + summary, ProgressMeter, adaptive params, random starts, try-catch, formatted output, elapsed time).
- **Ablation study**: ARGS, resume, summary, TeeIO, CSV raw + summary, per-iteration history, try-catch, formatted output, elapsed time.
- **Application**: Same as multi-start benchmark.
- **Extension**: Same as multi-start benchmark.
- **Figure generation**: Minimal — no ARGS parsing, no TeeIO, no CSV. Just read data + plot.
- **Table generation**: Minimal — read CSV, format LaTeX table, write to file.
- **Custom**: Ask user what they need.

Present the recommended set and let the user toggle features on/off.

#### Question 3: Dependencies

After determining features, check which packages are needed and not yet available in the project. Then ask:

> "This script needs [package list]. Should I add them to [Project.toml / deps.jl], or will you?"

If the agent handles it:
- **Style A**: Add to `Project.toml` `[deps]` section AND add `using PackageName` to the module file
- **Style B**: Add `using PackageName` to `deps.jl`

If the user handles it: note which packages are needed and move on.

See `references/dependency-guide.md` for the feature → package mapping.

### Phase 3: Generate

#### Step 1: Infrastructure Check

Before generating the script, ensure supporting files exist.

**`io_utils.jl`** — Required if TeeIO logging is selected:
- Check if `io_utils.jl` exists in `src/`
- If missing: create it with TeeIO, setup_logging, teardown_logging
- If exists but incomplete: add missing functions
- See `references/infrastructure-patterns.md` for canonical code

**Integration after creating/updating `io_utils.jl`:**
- **Style A**: Add `include("io_utils.jl")` to module file, add exports for `TeeIO`, `setup_logging`, `teardown_logging`
- **Style B**: Add `include("io_utils.jl")` to `includes.jl`

**`utils.jl`** — Required if the script needs shared helpers:
- `check_derivatives` → needed for verification scripts
- `elapsed_str` formatting → needed for scripts with elapsed time tracking
- Other project-specific helpers as needed
- See `references/infrastructure-patterns.md` for canonical code

**`results/` directories:**
- Create the output subdirectory for this script (e.g., `results/multistart/`, `results/ablation/`)
- Ensure `results/logs/` exists if TeeIO is selected

#### Step 2: Compose the Script

Assemble the script from the selected feature blocks. The script follows this structure:

```
1. Header comment block (goal, output, usage)
2. Load path / imports
3. ARGS parsing (if selected)
4. Setup logging (if TeeIO selected)
5. Configuration constants
6. Helper functions (if needed)
7. CSV constants and paths (if CSV selected)
8. Summary mode block (if selected) — exits early
9. Resume support block (if selected)
10. Main loop header (banner, config printout)
11. Main loop body (with try-catch, progress, CSV writes)
12. Cleanup (close CSV, print summary, teardown logging)
```

See `references/script-patterns.md` for the code block for each feature.

#### Step 3: Adapt to Project

Replace all placeholder names with project-specific values:
- Module name / include path
- Algorithm function name and config type
- Problem getter function
- Result field names
- CSV column names matching the project's data model
- Results directory names

#### Step 4: Verify

After generating, verify:
- [ ] All imports are available (either in module or deps.jl)
- [ ] io_utils.jl is included in the module/includes chain
- [ ] Results directory will be created by the script (mkpath)
- [ ] Script header comment has correct usage examples
- [ ] CSV headers match the @printf format strings (column count, types)

## Language Adaptation

Default language is **Julia**. If the user explicitly requests Python or MATLAB:

- **Python**: Adapt patterns using `argparse`, `csv` module, `logging`, `tqdm` for progress. TeeIO becomes a dual-handler logger. CSV I/O via `csv.writer` with flush.
- **MATLAB**: Adapt patterns using `inputParser`, `fprintf` for dual output, `readtable`/`writetable` for CSV.

The structural principles (header, ARGS, resume, two-mode, progress, CSV flush) transfer directly; only syntax changes.

## Reference Files

- `references/script-patterns.md` — Composable code blocks for each feature
- `references/infrastructure-patterns.md` — Canonical io_utils.jl and utils.jl code
- `references/dependency-guide.md` — Feature → package mapping, installation instructions
