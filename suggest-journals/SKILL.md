---
name: suggest-journals
description: Find suitable Q1–Q2 journals for publication. Searches Scimago, journal
  databases, and community sources. Filters by indexing (ISI/Scopus), access model,
  publisher exclusions, and response time. Use when targeting journals for a new paper.
invocation: user
---

# /suggest-journals — Journal Finder

Find suitable journals for a research paper. Gathers topic context, applies standing preferences (quartile, indexing, publisher exclusions), searches online sources, and produces a formatted note with ranked suggestions.

## Quick Start

- `/suggest-journals` — Start a new journal search
- `/suggest-journals review` — Review/update an existing journal suggestions note

## Workflow

### Phase 1: Context Discovery

Before asking questions, gather project context:

1. **Read the project's `CLAUDE.md`** to determine:
   - Research topic and field/subfield
   - Paper type (theoretical, computational, applied, mixed)
   - Target audience (optimization, numerical analysis, applied math, engineering, etc.)
   - Any previously mentioned target journals

2. **Scan the paper** (if `.tex` files exist):
   - Read the abstract and introduction for keywords and positioning
   - Note the balance of theory vs. computation vs. application
   - Check existing `\journal{}` or submission-related comments

3. **If no project context is available**, ask the user for:
   - Research topic and keywords (3–5 terms)
   - Field/subfield
   - Paper type (theoretical, computational, applied)

### Phase 2: Preferences

Present default preferences using AskUserQuestion and let the user override any:

| Preference | Default | Notes |
|---|---|---|
| **Quartile** | Q1–Q2 (Scimago) | Can relax to Q3 if needed |
| **Excluded publishers** | MDPI | Add others as needed |
| **Indexing** | ISI (Web of Science) + Scopus | Both required by default |
| **Access model** | Exclude open-access-only journals | Hybrid OA is fine |
| **Response time** | ≤ 6 months to first decision | Based on community reports |
| **Number of suggestions** | 10 | Can increase/decrease |
| **Include specific journals** | None | User can request specific journals be evaluated |
| **Exclude specific journals** | None | User can rule out journals |

Use a single AskUserQuestion with these defaults listed. The user can accept all defaults or specify overrides.

### Phase 3: Search & Report

#### Step 1: Search

Use web search to find candidate journals. Follow the strategy in `references/search-strategy.md`:

1. **Scimago SJR** — Search for the field/subfield category, filter by quartile
2. **Journal finder tools** — Use publisher journal finders (Elsevier, Springer, Wiley) with paper keywords
3. **Community sources** — Search for researcher discussions about journal experiences:
   - Reddit (r/academia, r/AskAcademia, field-specific subs)
   - ResearchGate discussions
   - Academic StackExchange
4. **Response time data** — Search for reported review times on journal review sites

#### Step 2: Filter

Apply the user's preferences to narrow the candidate list:

- Remove journals from excluded publishers
- Remove journals below the target quartile
- Remove journals not indexed in required databases
- Remove open-access-only journals (unless user allows them)
- Flag journals with reported response times exceeding the threshold

#### Step 3: Rank

Rank remaining journals by scope fit to the paper's topic. Consider:

- How well the journal's aims & scope match the paper's contribution
- Whether similar papers have been published there recently
- Impact factor and field normalization (SJR, CiteScore)
- Reported review turnaround

#### Step 4: Generate Report

Create the output note using the template in `references/output-template.md`:

- Save to `notes/journal_suggestions_YYYYMMDD.md`
- Include all search metadata (query, preferences, date)
- Present journals in a ranked table
- Add notes on scope fit and any caveats

Present the completed note to the user and highlight the top 3 recommendations with brief reasoning.

## Reference Files

- `references/search-strategy.md` — Web search patterns, URL structures, cross-checking methods
- `references/output-template.md` — Markdown template for the output note

## Limitations

- Journal metrics (IF, quartile, SJR) change annually — always note the year of the data
- Response times are self-reported by authors and vary widely
- Open access policies and APCs change frequently — verify on the journal website before submitting
- Scimago categories may not perfectly match the paper's interdisciplinary scope
- This skill searches for publicly available information; it cannot access paywalled databases
