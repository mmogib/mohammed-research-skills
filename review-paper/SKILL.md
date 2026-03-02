---
name: review-paper
description: Paper review and polish checklist for academic papers before submission.
  Walks through a 13-item universal checklist (notation, proofs, assumptions, abstract,
  introduction, style, etc.), then adds project-specific items interactively. Distributes
  tasks between Claude and the user, then Claude executes its assigned items.
  Use when reviewing, polishing, or doing a final pass on a paper before submission.
invocation: user
---

# /review-paper — Paper Review & Polish

Walk through a structured review of an academic paper before submission. Produces a tracked checklist note and systematically works through each item.

## Workflow

### Phase 1: Context Discovery

Before asking questions, gather project context:

1. Read the project's `CLAUDE.md` to understand: paper topic, structure, LaTeX setup
2. Locate `.tex` files — identify the main file and any `\input`/`\include` structure
3. Scan the paper to note what it contains:
   - Proofs? (items 2, 3 apply)
   - Named assumptions like (A1)–(An)? (item 3 applies)
   - Core mathematical derivations? (item 4 applies)
   - Computational experiments? (item 5 applies)
   - Algorithm box / pseudocode? (item 10 applies)
   - Figures and tables? (item 11 applies)
4. Check for existing review notes in `notes/`

### Phase 2: Universal Checklist

Generate `notes/review_checklist_YYYYMMDD.md` with applicable items from `references/checklist-items.md`.

Format:

```markdown
# Review Checklist — YYYY-MM-DD

## Items

1. **Notation & LaTeX style consistency**
2. **Proof review**
3. **Assumptions audit**
...

## Status

- [ ] 1. Notation & LaTeX style
- [ ] 2. Proof review
...
```

Skip items marked "(if any)" that don't apply based on Phase 1 findings. Renumber accordingly.

Present the checklist to the user for confirmation.

### Phase 3: Project-Specific Items

Ask: "Any project-specific review tasks to add?"

Examples of what the user might add:
- "Verify (A1)–(A5) are all cited correctly"
- "Check that Theorem 4.1 proof handles the boundary case"
- "Ensure the Signal Recovery discussion isn't redundant across sections"

Append these as numbered items after the universal ones.

### Phase 4: Task Distribution

Go through each item with the user using AskUserQuestion. For each, propose one of:

| Label | Meaning |
|-------|---------|
| **Claude** | Automatable — Claude does it fully (grep for patterns, diff bib entries, search banned words) |
| **Claude flags → User reviews** | Claude scans and flags potential issues, user makes final judgment |
| **User** | Needs human judgment (proof correctness, mathematical insight) |

Default assignments:

| Item | Default |
|------|---------|
| 1. Notation & LaTeX style | Claude |
| 2. Proof review | Claude flags → User reviews |
| 3. Assumptions audit | Claude flags → User reviews |
| 4. Core derivation completeness | User |
| 5. Computational verification mention | Claude flags → User reviews |
| 6. Abstract polish | Claude flags → User reviews |
| 7. Introduction tightening | Claude flags → User reviews |
| 8. Section transitions | Claude flags → User reviews |
| 9. Redundancy check | Claude flags → User reviews |
| 10. Algorithm/method presentation | Claude flags → User reviews |
| 11. Figure/table captions | Claude |
| 12. Bibliography cleanup | Claude |
| 13. Style pass | Claude |

Present defaults and let user adjust. User may also reassign project-specific items.

### Phase 5: Execute

Work through Claude's assigned items in checklist order:

1. **Before each item**: Mark it `in_progress` in the checklist note
2. **During**: Read the relevant `.tex` files, grep for patterns, report findings
3. **After**: Update the checklist note with findings and mark `[x]` with a brief status summary
4. **For "Claude flags" items**: List findings, then pause for user to confirm fixes

For items assigned to the user, skip them and note "Assigned to user" in the status.

After completing all Claude items, print a summary of remaining user tasks.

## Execution Guidelines Per Item

### 1. Notation & LaTeX style
- Grep for `\text{` (should be `\operatorname` or `\mathrm` for operators)
- Grep for `\( ` and `\)` (should use `$...$`)
- Grep for `\rightarrow` (should be `\to`)
- Grep for inconsistent bold/mathbb patterns
- Check theorem environment counters in preamble

### 5. Computational verification mention
- Search for "finite difference", "numerical verification", "gradient check" or similar
- If absent, flag the gap

### 11. Figure/table captions
- List all `\caption{...}` and check self-containedness
- Cross-reference all `\ref{fig:` and `\ref{tab:` with actual labels
- Flag any orphan figures/tables

### 12. Bibliography cleanup
- Extract all `\cite{...}` keys from `.tex` files
- Compare against `.bib` file entries
- Report: undefined references, orphan bib entries, duplicate authors

### 13. Style pass
- Grep for each banned word from the checklist
- Grep for weak openings ("It is ", "There are ")
- Grep for wordy phrases from the replacement table
- Report with line numbers and suggested replacements

## Reference Files

- `references/checklist-items.md` — Full universal checklist with detailed sub-items
