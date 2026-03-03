# Mohammed's Research Skills for Claude Code

Custom Claude Code skills for optimization algorithm research.

## Skills

| Skill | Slash Command | Description |
|---|---|---|
| `optimization-research-workflow` | `/optimization-research-workflow` | End-to-end workflow for optimization research: theory, Julia implementation, parameter tuning, benchmarking, and paper writing |
| `math-research-writer` | `/math-research-writer` | Writing rigorous, publication-ready mathematics papers with theorem/proof structure, notation consistency, and LaTeX formatting |
| `title-abstract` | `/title-abstract` | Drafting and reviewing titles and abstracts for academic papers in computational and applied mathematics |
| `init-project` | `/init-project` | Interactive scaffolding for new research projects: directory structure, CLAUDE.md files, Julia project setup (Style A or B), LaTeX template |
| `jcode-script` | `/jcode-script` | Experiment script generator with consistent patterns: ARGS parsing, CSV I/O, resume, TeeIO logging, progress bars, and more |
| `review-paper` | `/review-paper` | Paper review & polish checklist: 13-item universal checklist, project-specific items, task distribution, automated style/notation/bib checks |
| `suggest-journals` | `/suggest-journals` | Find suitable Q1–Q2 journals for publication: Scimago search, indexing/publisher/access filters, response time data |

## Setup

```bash
# Clone into Claude Code's skills directory
mkdir -p ~/.claude/skills
git clone https://github.com/mmogib/mohammed-research-skills.git ~/.claude/skills/mohammed-research-skills
```

Claude Code discovers skills automatically by searching `~/.claude/skills/` recursively for `SKILL.md` files.

## Related

- **Research toolkit:** [mmogib/research-toolkit](https://github.com/mmogib/research-toolkit) — Guides, templates, and conventions referenced by these skills. Clone it and point your project's `CLAUDE.md` to it.
