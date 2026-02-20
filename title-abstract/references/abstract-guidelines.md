# Abstract Guidelines for Mathematical Writing

## Length Requirements

| Document Type | Typical Limit | Notes |
|---------------|---------------|-------|
| Research paper (SIAM) | 250 words | One paragraph |
| Research paper (Springer) | 150-250 words | One paragraph |
| Research paper (Elsevier) | 100-250 words | Varies by journal |
| MS Thesis | 150-300 words | May be structured |
| PhD Thesis | 300-500 words | Often structured |
| Grant proposal | Agency-specific | Often 1 page |
| Conference paper | 100-200 words | Very concise |

## Core Principles

### 1. Must Stand Alone
The abstract is often read in isolation (databases, search results). Include:
- Problem context
- Main contribution
- Key results
- Significance

### 2. State Results, Not Methods of Proof
- Bad: "We prove this using a contrapositive argument and spectral analysis"
- Good: "We prove that the rate improves from O(k^{1/(1-q)}) to O(k^{2/(1-q)})"

### 3. Lead with Contribution
Open with what you accomplish, not background.
- Weak: "Convex optimization is important in many applications..."
- Strong: "We develop an adaptive-metric framework for..."

### 4. Be Specific and Quantitative
- Weak: "We improve convergence"
- Strong: "We achieve 13-31% fewer iterations"

## Structure Template

### For Research Papers (150-250 words)

**Paragraph structure:**

1. **Opening** (1-2 sentences): Main contribution/what we do
2. **Context** (1 sentence, optional): Why this matters or gap addressed
3. **Key results** (2-4 sentences): Main theorems/findings
4. **Validation** (1 sentence, optional): Experiments/applications
5. **Significance** (1 sentence): Impact or foundation laid

**Example structure:**
```
We develop [main contribution]. [Optional: This addresses gap/extends prior work.]

[Result 1 - most important theorem/finding.]
[Result 2 - supporting result.]
[Result 3 - if needed.]

[Experimental validation or applications.]
[Concluding significance statement.]
```

### For Theses (300-500 words)

May use IMRAD structure with paragraph breaks:
- **Introduction**: Problem and motivation
- **Methods**: Approach and techniques
- **Results**: Main findings
- **Discussion**: Implications and contributions

## Mathematical Content in Abstracts

### Do Include
- Key complexity/rate expressions: O(k^2), O(n log n)
- Important bounds or inequalities (if central)
- Notation that is standard: R^n, L^p

### Avoid
- Undefined symbols specific to your paper
- Equation numbers or theorem references
- Complex multi-line equations
- Citations (if unavoidable, write out full reference)

**SIAM guideline**: "Mathematical formulas and bibliographic references should be kept to a minimum; bibliographic references must be written out in full, not given by number."

## Opening Sentences

### Effective Openings
- "We develop..." / "We introduce..."
- "We prove that..." / "We establish..."
- "We analyze..." / "We study..."
- "This paper presents..." / "This work introduces..."

### Openings to Avoid
- "In this paper, we..." (weak, delays content)
- "This paper is about..." (vague)
- "We are interested in..." (unfocused)
- "It is well known that..." (delays contribution)
- Starting with extensive background

### Better Patterns
- Start with the contribution, add context after
- "We develop X. This extends prior work on Y by..."
- "We prove X, settling an open question from [Author]."

## Common Mistakes

### 1. Excessive Background
Don't spend >20% of words on motivation/background.

### 2. Proof Techniques in Abstract
State what you prove, not how.
- Remove: "by exploiting the linear isometric structure"
- Remove: "via a contrapositive argument"

### 3. Undefined Abbreviations
Define on first use or avoid entirely.
- Bad: "We analyze the CVOP algorithm"
- Good: "We analyze convex vector optimization problems (CVOPs)"

### 4. Vague Results
Be specific about what is proven.
- Bad: "We improve convergence"
- Good: "We prove convergence rate O(k^{2/(1-q)})"

### 5. Missing Quantitative Results
Include numbers when available.
- Bad: "Experiments show improvement"
- Good: "Experiments show 13-31% reduction in iterations"

### 6. Exceeding Word Limit
Count words before submission. Most journals enforce limits strictly.

### 7. Not Self-Contained
Reader should understand the contribution without reading the paper.

## Revision Checklist

1. [ ] Within word limit?
2. [ ] Opens with contribution (not background)?
3. [ ] All key contributions mentioned?
4. [ ] Specific, quantitative results included?
5. [ ] No undefined abbreviations?
6. [ ] No proof methodology details?
7. [ ] No equation numbers or internal references?
8. [ ] Minimal citations (written out if included)?
9. [ ] Readable by someone in related field?
10. [ ] Keywords for searchability present?
