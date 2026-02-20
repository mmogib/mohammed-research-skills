# How to Write Effective CLAUDE.md Files

CLAUDE.md files are the primary way Claude Code understands your project. Well-written CLAUDE.md files dramatically improve session quality by providing context, rules, and current status.

---

## Project-Level CLAUDE.md

Located at the project root. Provides high-level context about the entire project.

### Structure

```markdown
# PROJECT_NAME Project

## Overview
One paragraph: what algorithm, what problem class, where the paper is.

## Structure
Directory tree showing top-level folders with brief descriptions.

## Rules
Numbered list of constraints:
1. DO NOT compile LaTeX.
2. Respect .claudeignore.
3. Code execution instructions (Python/Julia paths).
4. Available skills/tools.
5. Notes workflow conventions.

## Pointers
Bullet list of cross-references:
- Implementation details: see `jcode/CLAUDE.md`
- Paper: `paper/main.tex` — where to find key sections
- Reference algorithms: list with file locations

## Current Status (date)

### Completed
Bullet list of completed milestones with dates and brief descriptions.
Include key metrics and outcomes.

### Key findings
Organized by experiment/analysis.
Include the most important numerical results.

### Next steps
Numbered list of immediate next actions.

### Results & analysis files
Bullet list mapping result types to file locations.
```

### Key principles

1. **Keep Current Status updated.** This is the most-read section. Update after every major milestone.
2. **Include key findings with numbers.** "MISTTDFPM has fewest iterations at every dimension: mean 8.9 vs 14.6" is more useful than "MISTTDFPM performs well".
3. **Cross-reference liberally.** Point to `jcode/CLAUDE.md` for implementation details, to notes files for analysis.
4. **Rules are enforced.** Claude follows rules in CLAUDE.md strictly. Use this for project conventions.

---

## Implementation-Level CLAUDE.md

Located at `jcode/CLAUDE.md`. Provides detailed implementation context.

### Structure

```markdown
# ALGORITHM_NAME Implementation

## Overview
What algorithm, what paper, what problem it solves.

## File Structure
Directory tree with every source file and script described.

## Important Rules
### Dependencies
Where to put `using` statements.
### Running Scripts
Whether Claude should run scripts automatically.

## Usage
Code examples showing how to construct and run the algorithm.
Include presets, parameter overrides, reference algorithms.

### Presets
Table of available presets with descriptions.

## Algorithm Summary
Table mapping algorithm steps to implementation functions.

## Parameters
### Constructor
Table of all parameters: name, default value, description, constraints.
### Line Search Types / Direction Types
Tables for categorical parameters.

## Key Components
Description of each source file and its public functions.

### solve() Common Kwargs
Table of solve() parameters shared across all algorithms.

## Dependencies
Code block showing deps.jl contents.
List script-only dependencies.

## Running Scripts
Code examples for every script with ARGS dispatch.
### Script ARGS reference
Table: Script | Valid parts | Tiers/Phases.

## Connection to Paper
Where to find the algorithm, theorems, lemmas in the paper.
```

### Key principles

1. **Document every parameter.** A table with name, default, description is essential for parameter search.
2. **Show usage examples.** Claude learns from examples. Show constructor calls, solve calls, iterator usage.
3. **ARGS reference table.** Quick lookup for which parts each script supports.
4. **Connection to paper.** Helps when writing results sections or checking implementation.

---

## Status Tracking Conventions

### Completed items format
```markdown
- **Brief description** (date): More details. Key metric. File location.
```

### Key findings format
```markdown
### Key findings — Experiment Name
- **Headline result**: Numbers in context.
- **Head-to-head ratios** (<1 = our algo wins):
  - vs Reference1: 0.63x iters, 0.70x fevals, **0.76x CPU**
```

### Next steps format
```markdown
### Next steps
1. **Action verb + description** — context if needed.
2. **Action verb + description** — depends on step 1.
```

---

## Anti-Patterns

1. **Stale status.** A CLAUDE.md with outdated "Next steps" is worse than no status section. Update or remove.
2. **Too much detail.** CLAUDE.md is context, not documentation. Don't paste entire file contents.
3. **Missing rules.** If you want Claude to behave a certain way, put it in Rules. Don't assume.
4. **No pointers.** Without cross-references, Claude doesn't know where to look.
5. **Vague findings.** "Results are good" tells Claude nothing. Include numbers.
