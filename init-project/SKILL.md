---
name: init-project
description: Interactive scaffolding for new research projects. Creates the full
  directory structure (paper/, jcode/, notes/, refs/), populates CLAUDE.md files,
  sets up Julia project with chosen architecture (Style A Module or Style B Flat
  Include), and copies templates from the research toolkit.
invocation: user
---

# /init-project — Interactive Research Project Scaffolding

You are setting up a new research project for Mohammed. Follow these steps exactly.

## Step 1: Find the Research Toolkit

The research toolkit contains templates, guides, and conventions. Find it:

1. Search for a directory containing `CLAUDE.md` with the text "Mohammed's Research Toolkit" in these locations (use Glob and Grep, search in parallel):
   - `D:/Dropbox/Research/research-toolkit/`
   - `~/research-toolkit/`
   - `~/Research/research-toolkit/`
   - `~/Dropbox/Research/research-toolkit/`
   - Siblings of the current directory: `../research-toolkit/`
   - Parent's siblings: `../../research-toolkit/`

2. If found, store the path and move to Step 2.

3. If NOT found, ask the user:
   - Option A: "Enter the path to your research-toolkit directory"
   - Option B: "Clone from GitHub" — run `git clone https://github.com/mmogib/research-toolkit.git` to a location the user specifies, then use that path.

## Step 2: Detect Existing Content

Check the current working directory:
- Does `paper/main.tex` exist? If yes, note it — do not overwrite it.
- Does `CLAUDE.md` exist? If yes, warn the user and ask if they want to continue (this may be an existing project).
- Is the directory empty (or nearly empty)? Note this.

Report what you found and proceed.

**LaTeX template handling:**
- If `paper/main.tex` does NOT exist: create it from `templates/main.tex.template`, filling in the title from the user's answer in Step 3. Also create an empty `paper/references.bib`.
- If `paper/main.tex` DOES exist: the user already has preliminary notes. Do NOT touch it. Offer to copy the LaTeX template as `paper/template_reference.tex` so the user can pull preamble/boilerplate from it if needed.

## Step 3: Ask Project Questions

Ask the user the following interactively using AskUserQuestion. Ask one group at a time, not all at once:

**Group 1 — Project identity:**
- Project title (the paper title, or a working title)
- Short codename for the project directory/module (e.g., "CondGVOP", "MISTDFPM")

**Group 2 — Coding architecture:**
- Style A (Module Package) or Style B (Flat Include)?
  - Style A: Code in `module ... end`, explicit exports, `@kwdef` configs, `@testset` tests. Best for reusable libraries, multiple algorithms, namespace isolation.
  - Style B: No module wrapper, `include("src/includes.jl")`, iterator protocol, preset system, multi-solver benchmarking. Best for single-algorithm, rapid prototyping, many variants.

**Group 3 — Project scope (optional, can be filled later):**
- Co-authors (names and emails), or use default (Mohammed only)?
- Brief description of the core problem (one sentence) — or skip for now?

## Step 4: Read Templates

Based on the chosen style, read the appropriate templates from the toolkit directory:

**Always read:**
- `templates/CLAUDE.md.template`
- `templates/jcode-CLAUDE.md.template`
- `templates/Project.toml.template`
- `templates/main.tex.template` (LaTeX starter with preamble, theorem envs, boilerplate)
- `templates/script_benchmark.jl` (for reference, not to copy verbatim)

**Style A additionally:**
- `templates/module_template.jl`
- `templates/runtests.jl.template`

**Style B additionally:**
- `templates/includes_template.jl`
- `templates/deps_template.jl`
- `templates/iterator_solver_template.jl`

## Step 5: Generate Project Structure

Create the following directory tree:

```
./
├── CLAUDE.md                     ← Populated from template + user answers
├── paper/
│   ├── main.tex                  ← From LaTeX template (SKIP if already exists)
│   ├── references.bib            ← Empty file (Zotero will populate)
│   └── imgs/                     ← Empty directory
├── jcode/
│   ├── CLAUDE.md                 ← Populated from template + user answers
│   ├── Project.toml              ← Populated with correct module name
│   ├── src/                      ← Style-dependent starter files
│   ├── scripts/
│   │   └── s10_smoke_test.jl     ← Generic smoke test stub
│   ├── test/                     ← Style A: runtests.jl; Style B: empty
│   └── results/
│       └── logs/
├── notes/
│   └── done/
└── refs/                         ← Reference papers (PDFs) for Claude to read
```

### File Population Rules

**CLAUDE.md** (project-level):
- Fill in: project title, codename, authors, toolkit path, structure diagram
- Include the toolkit reference line: `See [toolkit-path] for coding style, templates, and workflow guides.`
- Include skills reference: `Skills: ~/.claude/skills/mohammed-research-skills/`
- Include all standard rules (never run scripts, never compile LaTeX, etc.)
- Leave sections like "Key Contributions" and "Paper Sections" with placeholder text for the user to fill in

**jcode/CLAUDE.md**:
- Fill in: module name, structure (matching chosen style)
- Leave algorithm-specific sections as placeholders

**jcode/Project.toml**:
- Set `name` to the codename
- Generate a UUID via Julia or leave as placeholder comment
- Include standard dependencies (JuMP, HiGHS, LinearAlgebra, Printf, Random, Test)
- Comment out optional deps (Ipopt, LazySets, Plots, ProgressMeter) with notes

**jcode/src/** (Style A):
- `{ModuleName}.jl` — module file with includes and exports (from template)
- `types.jl` — empty file with header comment: `# Type definitions for {ModuleName}`
- `problems.jl` — empty file with header comment
- `utils.jl` — empty file with header comment
- `io_utils.jl` — TeeIO implementation (copy from template, this is reusable as-is)

**jcode/src/** (Style B):
- `includes.jl` — from template
- `deps.jl` — from template
- `algorithm.jl` — iterator solver template (with TODOs marked)
- `problems.jl` — empty file with header comment
- `benchmark.jl` — empty file with header comment

**refs/**:
- Empty directory. Mohammed downloads reference papers (PDFs) here when Claude needs to consult them.
- Workflow: Claude asks "Can you download [paper] into refs/?", Mohammed downloads it, Claude reads it with the Read tool.
- Typical uses: verifying a cited formula, checking a proof technique, understanding a referenced algorithm.

**jcode/scripts/s10_smoke_test.jl**:
- Generic smoke test: load module/includes, run solver on first problem, print pass/fail
- Adapt load pattern to chosen style (Style A: `using`, Style B: `include`)

**jcode/test/runtests.jl** (Style A only):
- From template, with module name filled in

## Step 6: Summary

After generating all files, print a summary:
- List all files created (with full paths)
- List any files skipped (because they already existed)
- Remind the user of next steps:
  1. Fill in the placeholder sections in CLAUDE.md
  2. Define test problems in `jcode/src/problems.jl`
  3. Implement the algorithm in `jcode/src/`
  4. Start with `s10_smoke_test.jl` to verify basic functionality
  5. If the toolkit was cloned fresh, consider customizing it

## Important Rules
- NEVER overwrite existing files. If a file exists, skip it and report that you skipped it.
- NEVER create `paper/main.tex` if it already exists — the user's preliminary notes are there.
- Use the Write tool for new files. Use Edit only for existing files (which should not happen in a fresh project).
- All generated files should have real content, not just "TODO" — fill in as much as possible from the user's answers. Use placeholders only for information you genuinely don't have yet.
- The generated CLAUDE.md files should be immediately useful to Claude in future sessions.
