# Output Template

Use this template when generating the journal suggestions note. Save to `notes/journal_suggestions_YYYYMMDD.md`.

## Template

```markdown
# Journal Suggestions — YYYY-MM-DD

## Query
- **Topic**: [research topic and keywords]
- **Field**: [field/subfield]
- **Paper type**: [theoretical / computational / applied / mixed]
- **Preferences**: Q1–Q2, ISI+Scopus, no MDPI, no OA-only, ≤6mo response
- **Overrides**: [any user-specified overrides, or "None"]

## Journals

| # | Journal | Publisher | Quartile | IF | SJR | Scope Fit | Response Time | Indexing | Access | Link |
|---|---------|-----------|----------|----|-----|-----------|---------------|---------|--------|------|
| 1 | [name] | [publisher] | Q1 | [IF] | [SJR] | High | ~3 months | ISI, Scopus | Hybrid | [url] |
| 2 | ... | ... | ... | ... | ... | ... | ... | ... | ... | ... |

### Column Definitions
- **Quartile**: Scimago quartile in the most relevant subject category (note category if ambiguous)
- **IF**: Journal Impact Factor from JCR (most recent available year)
- **SJR**: Scimago Journal Rank indicator
- **Scope Fit**: High / Medium / Low — how well the journal's aims match this paper
- **Response Time**: Approximate time to first editorial decision (from community reports)
- **Indexing**: Which databases index this journal (ISI = Web of Science, Scopus, etc.)
- **Access**: Subscription / Hybrid / Open Access

## Top Recommendations

### 1. [Journal Name]
- **Why**: [1–2 sentences on scope fit and strengths]
- **Considerations**: [any caveats — APC, competition, narrow scope, etc.]

### 2. [Journal Name]
- **Why**: [1–2 sentences]
- **Considerations**: [any caveats]

### 3. [Journal Name]
- **Why**: [1–2 sentences]
- **Considerations**: [any caveats]

## Notes
- Metrics sourced from Scimago/JCR [year]. Verify current values before submitting.
- Response times are approximate, based on community reports (Reddit, SciRev, ResearchGate).
- Check each journal's current submission guidelines and APC (if applicable) before deciding.

## Sources
- [list URLs consulted during the search]
```

## Usage Notes

- Replace all `[bracketed]` placeholders with actual data
- Remove the "Column Definitions" section if presenting to user in conversation (keep it in the saved note)
- If a data point is unavailable, write "N/A" rather than guessing
- Always include the year of metrics data in the Notes section
- The Sources section should list the actual URLs consulted during research
