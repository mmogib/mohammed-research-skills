# Universal Paper Review Checklist

Complete items in order. Items marked "(if any)" may not apply to every paper — skip if not relevant.

## 1. Notation & LaTeX Style Consistency

- [ ] Consistent use of `\mathbb{R}`, `\subseteq`, operators throughout
- [ ] All math operators defined via `\DeclareMathOperator` (not ad hoc `\text{...}`)
- [ ] Theorem/Lemma/Proposition/Remark environments use shared counter
- [ ] Consistent notation for: inner product, norm, sets, mappings
- [ ] No bare `i` or `j` in subscripts — use `_i` not `_ i`
- [ ] Inline math uses `$...$` consistently (no `\(...\)` mixing)
- [ ] Standard shorthands: `\to` not `\rightarrow`, `\R` not `\mathbb{R}` (if macro defined)
- [ ] Bold vectors/matrices consistent: `\mathbf` or `\bm`, not mixed
- [ ] Spacing around operators: no spurious `\,` or `\;` in subscripts/superscripts
- [ ] Tildes, hats, bars applied consistently to the same objects
- [ ] Capitalization in theorem names matches throughout

## 2. Proof Review

For each theorem, lemma, proposition:
- [ ] All assumptions explicitly cited (e.g., "By (A3), ...")
- [ ] Every inequality chain is justified (triangle inequality, Cauchy-Schwarz, etc.)
- [ ] No hidden assumptions (e.g., using continuity that hasn't been assumed)
- [ ] Limit arguments are rigorous (subsequences, compactness invoked explicitly)
- [ ] Upper semicontinuity vs. lower semicontinuity used correctly (if any)
- [ ] Convergence mode specified (pointwise, uniform, in norm)
- [ ] Quantifiers are correct and in the right order ("for all ... there exists ...")

## 3. Assumptions Audit

- [ ] All assumptions (A1), (A2), ... are used somewhere in the paper
- [ ] No assumption is redundant (implied by another)
- [ ] Each theorem/lemma states exactly which assumptions it needs
- [ ] No hidden additional assumptions in proofs
- [ ] No forward references to assumptions not yet stated

## 4. Core Derivation Completeness (if any)

- [ ] Key mathematical formulas are explicitly derived, not hand-waved
- [ ] Chain rules, product rules, or composition rules cited or shown where needed
- [ ] Regularity or qualification conditions verified where required
- [ ] Reader can reconstruct the derivation from what is written

## 5. Computational Verification Mention (if any)

- [ ] Paper mentions that key computed quantities were verified (e.g., finite differences, unit tests, known solutions)
- [ ] Verification methodology briefly described (step size, tolerance, etc.)
- [ ] Any expected failures at edge cases noted (e.g., nondifferentiability points)

## 6. Abstract Polish

- [ ] States the problem class
- [ ] Lists key contributions (matching the introduction)
- [ ] Mentions experimental scope (number of problems, key findings) if applicable
- [ ] No forward references or citations
- [ ] Under journal word limit

## 7. Introduction Tightening

- [ ] No paragraphs longer than 8-10 lines
- [ ] Each paragraph has one clear purpose
- [ ] Literature review is organized thematically (not a list dump)
- [ ] Contributions list matches what the paper actually delivers
- [ ] No repetition between intro subsections

## 8. Section Transitions

- [ ] Each section begins with a brief contextual sentence connecting to the previous section
- [ ] Reader can follow the logical flow without backtracking
- [ ] No abrupt topic changes

## 9. Redundancy Check

- [ ] Same information not stated in multiple sections
- [ ] If material is discussed in detail later, use a brief mention + forward reference earlier
- [ ] No circular cross-references (A cites B, B cites A for the same fact)

## 10. Algorithm/Method Presentation (if any)

- [ ] Algorithm environment is self-contained (all notation defined or referenced)
- [ ] Input/output clearly stated
- [ ] Step numbering is unambiguous
- [ ] Termination criterion explicitly stated
- [ ] Subroutines (line search, subproblem) clearly specified or referenced

## 11. Figure & Table Captions

- [ ] Captions are self-contained (reader understands without reading main text)
- [ ] Tables have units where applicable
- [ ] Figures have axis labels
- [ ] All figures/tables are referenced in the text
- [ ] No orphans (referenced but not present, or present but not referenced)

## 12. Bibliography Cleanup

- [ ] All `\cite` keys resolve (no undefined references)
- [ ] No orphan bib entries (in .bib but never cited) — optional cleanup
- [ ] Author names consistent (no duplicate entries for same paper)
- [ ] Year, journal, volume, pages present for published papers
- [ ] Preprints marked as such

## 13. Style Pass (LAST)

### Banned/flagged words
Search for and eliminate or replace:
- AI-sounding filler: "robust", "crucial", "comprehensive", "streamline", "leverage", "fundamental", "notably", "furthermore", "moreover", "noteworthy", "pivotal", "vital"
- Filler hedging: "it is worth noting that", "it should be mentioned that", "it is important to note that", "it goes without saying"
- Vague intensifiers: "significant(ly)" (when not statistical), "critical", "key" (as adjective filler), "convincingly", "effectively" (when adding nothing)

### Structural patterns
- [ ] No named-paragraphs with bold labels
- [ ] No sentences starting with "It is..." / "There are..." (weak openings) — rewrite with active subject
- [ ] No adverb stacking ("very significantly improved")
- [ ] No redundant qualifiers ("completely unique", "very optimal", "fully general")

### Wordy phrases → shorter alternatives
| Replace | With |
|---------|------|
| in order to | to |
| due to the fact that | because |
| a large number of | many |
| in the context of | in / for |
| with respect to | for / over |
| is able to | can |
| make use of | use |
| on the basis of | based on |
| for the purpose of | to / for |
| in the case of | if / when |
| it can be seen that | (delete — state the result directly) |
| a sufficient condition for | suffices for |

### Final checks
- [ ] No repeated words in close proximity (same word twice within 2-3 lines)
- [ ] Consistent tense: present for math ("we define", "it follows"), past for experiments ("we tested", "the algorithm converged")
- [ ] Final proofread: missing periods, incomplete sentences, double spaces, missing colons before lists/displays
- [ ] Sentences are direct and specific — every sentence earns its place
