---
name: math-research-writer
description: Specialized skill for writing high-quality academic mathematics research papers. Assists with rigorous theorem development, proof structure, mathematical notation, literature reviews in optimization/variational analysis, citation management, and peer-reviewed publication workflow. Supports nonlinear programming, variational inequalities, proximal algorithms, and neural network optimization research.
---

# Mathematics Research Writer

This skill acts as your dedicated research partner for writing rigorous, publication-ready mathematics papers while maintaining mathematical precision and academic standards.

## When to Use This Skill

- Writing research papers on computaional and applied mathematics, particularly on optimization theory and numerical optimization.
- Developing rigorous proofs and theorem statements
- Structuring scientific/mathematical papers
- Creating detailed algorithm descriptions with convergence analysis
- Organizing literature reviews
- Managing mathematical notation and LaTeX formatting
- Preparing papers for peer-reviewed mathematics journals
- Writing course materials with rigorous mathematical content
- Collaborating on multi-author research papers
- Revising papers for clarity, rigor, and publication standards
- Designing numerical experiments and computational sections
- Writing abstracts and introductions that contextualize results

## What This Skill Does

1. **Theorem & Proof Structure**: Organizes mathematical results with clear assumptions, statements, and proofs
2. **Notation Consistency**: Ensures mathematical notation is consistent, standard, and well-defined
3. **Literature Review**: Organizes citations and contextualizes your work within the mathematical landscape
4. **Convergence Analysis**: Reviews convergence proofs, rates, and theoretical guarantees
5. **Algorithm Presentation**: Structures algorithmic content with pseudocode, complexity analysis, and examples
6. **LaTeX Optimization**: Validates LaTeX formatting and mathematical expression presentation
7. **Academic Voice**: Maintains formal, rigorous mathematical writing style
8. **Peer Review Preparation**: Addresses common reviewer concerns about rigor and clarity
9. **Numerical Experiments**: Organizes computational results and empirical validation sections
10. **Publication Workflow**: Guides preparation for specific mathematical journals

## How to Use

### Setup Your Research Environment

Create a dedicated folder for your paper:
```
mkdir ~/research/paper-title
cd ~/research/paper-title
```

Create your draft file:
```
touch paper-draft.md
```

Or create a LaTeX-based project:
```
touch main.tex
touch references.bib
mkdir imgs
```
so latex structure becomes
```
---
  - imgs # to contain any images or pdf included in the paper
  - main.tex
  - references.bib
```
for main.tex structure and content see [template](#latex-template) below

Open Claude Code from this directory and start writing.

### Basic Workflow

1. **Structure your paper outline**:
```
Help me create an outline for a paper on [topic: e.g., accelerating algorithms for variational inclusions]
```

2. **Draft and organize main theorems**:
```
I have the following theorem. Help me state it precisely with clear assumptions and implications.
```

3. **Review proof structure**:
```
Review this proof for rigor, logical flow, and gaps.
```

4. **Organize literature review**:
```
Research the recent work on [topic] and structure a literature review section with proper citations.
```

5. **Refine and polish**:
```
Review this section for mathematical clarity, notation consistency, and publication readiness.
```

## Instructions

When a user requests mathematics research assistance:

### 1. Understand the Research Project

Ask clarifying questions:
- What is the main mathematical problem or innovation?
- What theorems or results are central to the paper?
- What is the mathematical context? (optimization, analysis, numerical methods, etc.)
- Who is the target audience? (optimization specialists, numerical analysts, general mathematicians)
- What mathematical framework? (convex analysis, monotone operator theory, variational methods, etc.)
- Any existing proofs or lemmas that form the foundation?
- Is this for a specific journal or conference? (Journal of Optimization Theory and Applications, SIAM, etc.)
- Which areas need the most work? (proofs, notation, experimental validation, literature positioning)

### 2. Collaborative Paper Outlining

Help structure mathematics papers with mathematical precision:

```markdown
# Research Paper Outline: [Title]

## Abstract
- **Key innovation**: [Main theoretical contribution]
- **Problem addressed**: [Mathematical problem]
- **Solution approach**: [High-level methodology]
- **Main results**: [Central theorems with convergence rates]
- **Keywords**: [5-6 relevant mathematical keywords]

## Introduction
- **Motivation**: Problem context and importance
- **Literature landscape**: How this builds on/differs from existing work
- **Main contributions**:
  - Contribution 1 with theorem reference
  - Contribution 2 with specific novelty
  - Contribution 3 with comparison to prior art
- **Paper organization**: Section-by-section roadmap
- **Notation**: Early definition of key symbols

## Preliminaries / Background
- **Definition**: [Mathematical object 1]
  - Key properties
  - Standard references [cite]
- **Definition**: [Mathematical object 2]
  - Relevant lemmas
  - Connection to main problem
- **Known results**: Existing theory you build upon
- **Gap in knowledge**: What you address

## Main Results / Theory

### Lemmas (Supporting Results)
- **Lemma 1**: [Statement]
  - Proof outline or reference
- **Lemma 2**: [Statement]

### Proposition 1: [Name/Description]
- **Assumptions**: [Clear list of conditions]
- **Statement**: [Precise mathematical result]
- **Interpretation**: Why this matters
- **Proof sketch**: [High-level proof idea]
- **Key lemmas needed**: [Reference]

### Proof of Proposition 1
- **Step 1**: [Logical stage]
- **Step 2**: [Next stage]
- **Key insight**: [Crucial moment]
- **Conclusion**: [Final step]

## Main Results
### Theorem 1: [Name/Description]
- **Assumptions**: [Clear list of conditions]
- **Statement**: [Precise mathematical result]
- **Interpretation**: Why this matters
- **Proof sketch**: [High-level proof idea]
- **Key lemmas needed**: [Reference]

### Proof of Theorem 1
- **Step 1**: [Logical stage]
- **Step 2**: [Next stage]
- **Key insight**: [Crucial moment]
- **Conclusion**: [Final step]

### Theorem 2: [Name/Description]
- **Relationship to Theorem 1**: [How they connect]
- **Assumptions**: [Conditions]
- **Statement**: [Result]
- **Convergence rate**: [Explicit rate if applicable]

### Proof of Theorem 2
- [Similar structure]





## Algorithm / Method (if applicable)

### Algorithm [Name]
- **Input**: [What the algorithm takes]
- **Pseudocode**:
  ```
  [Structured pseudocode]
  ```
- **Complexity Analysis**:
  - Iteration complexity: [per iteration]
  - Overall complexity: [total]
  - Memory requirements: [space]
- **Convergence Guarantee**: [Reference to theorem]

## Numerical Experiments

### Experiment 1: [Name]
- **Objective**: Validate Theorem X
- **Setup**: [Problem instances, parameters]
- **Results**: [Summary of outcomes]
- **Interpretation**: [What results show]

### Experiment 2: [Comparison Study]
- **Baseline methods**: [Algorithms compared against]
- **Test problems**: [Datasets/problem classes]
- **Metrics**: [Convergence rate, CPU time, accuracy]
- **Findings**: [Key comparisons]

## Literature Review / Related Work
- **Classical results**: [Foundational work - cite]
- **Recent advances**: [Recent papers building on these ideas]
- **Our contribution**: [How this paper advances the field]
- **Open questions**: [What remains to be done]

## Conclusion
- **Summary of results**: [Main theorems]
- **Significance**: [Impact and applications]
- **Future work**: [Natural next directions]
- **Broader implications**: [Connection to related areas]

## References
- [To be filled with citations]

## Appendices (if needed)
- Additional proofs
- Extra numerical results
- Extended examples
```

**Iterate on outline**:
- Ensure logical mathematical flow
- Check that all definitions precede their use
- Verify theorem assumptions are clear and achievable
- Identify research gaps and experimental needs

### 3. Conduct Literature Research

When user requests research on a mathematical topic:

- Search for recent papers in optimization, variational analysis, proximal methods, etc.
- Find key references that establish the mathematical foundation
- Identify competing approaches or related results
- Extract theorem statements, convergence rates, and key assumptions
- Assess how the user's work positions relative to literature

Example output:

```markdown
## Literature Review: Accelerated Proximal Methods

### Foundational Work
1. **Nesterov (2004)**: Introduced accelerated gradient methods achieving O(1/k²) rate
   - Reference: A. Nesterov, "Introductory Lectures on Convex Optimization"
   - Key result: For smooth convex functions
   - Why it matters: Established benchmark for acceleration

2. **Rockafellar (1976)**: Monotone operator splitting and proximal methods
   - Reference: R. T. Rockafellar, "Monotone operators and the proximal point algorithm"
   - Foundation for: Variational inequality solution methods

### Recent Advances (2020-2026)
1. **Author X, et al. (2024)**: State-dependent scaling for proximal algorithms
   - Innovation: Adaptive step-sizes based on problem structure
   - Convergence: O(1/k) for strongly monotone operators
   - Application areas: [List relevant problems]

2. **Author Y (2023)**: Splitting methods with momentum
   - Technique: Nesterov acceleration in forward-backward splitting
   - Comparison to your work: [How does your result compare?]

### Open Questions Your Paper Addresses
- Convergence analysis under weaker assumptions
- Extension to variational inequalities with composite structure
- Computational efficiency improvements
- [Your specific contributions]

### Positioning Your Work
- Your paper advances state-of-the-art by: [Key innovation]
- Extends prior results of [Author] by: [Specific extension]
- Addresses open question from: [Prior work]
```

### 4. Theorem & Proof Structure

When user shares theorem statements or proofs:

**Theorem Clarity Analysis**:
- Clear assumption list? ✓/✗
- Precise statement? ✓/✗
- Interpretation explained? ✓/✗
- Comparison to prior results?
- Assumptions necessary and sufficient?

**Suggested Improvements**:

```markdown
## Theorem Structure Review

### Current Statement
> [User's theorem as stated]

### Clarity Issues
- Assumption A is vague: "convergent" → specify convergence type
- Variable notation introduced inconsistently
- Result interpretation not provided

### Suggested Revision

**Theorem 1** (Convergence of Algorithm X): 
Let {x_k} be the sequence generated by Algorithm 1 with step-size α ∈ (0, 1/L).
Assume f is L-smooth and convex. Then the sequence {f(x_k)} converges to f(x*) at rate:

f(x_k) - f(x*) ≤ C/(k+1)

where C depends on f(x_0) and the problem structure.

### Why This Version Works Better
- Explicit assumptions
- Clear variable definitions
- Precise convergence statement with rate
- Parameter restrictions clearly stated

### Proof Outline Check
1. Step 1 uses Assumption A correctly? ✓
2. Step 2 follows logically from Step 1? ✓
3. Lemma 3 applied correctly? ✓
4. Conclusion matches statement? ✓
```

**Proof Review Process**:

```markdown
# Proof Review: [Theorem Name]

## Logical Structure ✓
- Initial setup clear
- Each step justified with previous results
- No logical gaps identified
- Conclusion directly follows from final step

## Rigor Assessment

### What Works Well
- Clear use of assumptions
- Proper citation of lemmas
- Explicit inequality chains

### Areas for Strengthening
- Line X: Justification missing for inequality
- Line Y: Monotonicity assumption not invoked
- Define variable Z before use in Step 3

### Specific Suggestions

Original:
> "By smoothness, we have f(x_k+1) ≤ ..."

Improved:
> "By L-smoothness of f, for any step-size α ≤ 1/L, we have f(x_k+1) ≤ ..."

Why: Makes the assumption-implication connection explicit

## Technical Correctness
- No calculation errors detected
- Inequality directions preserved
- Inequalities chain properly
- Exponents and constants handled correctly

## Mathematical Presentation
- Standard notation used
- Consistency with defined terms
- Proper use of quantifiers

Ready for peer review!
```

### 5. Mathematical Notation & LaTeX Review

When user requests notation review or LaTeX help:

**Notation Consistency Check**:
```markdown
# Notation Review

## Notation Table

| Symbol | Definition | Usage | Notes |
|--------|-----------|-------|-------|
| x | Decision variable | Primary | ℝⁿ |
| f(x) | Objective function | Cost | Convex, smooth |
| ∂f(x) | Subdifferential | Optimality | Non-smooth analysis |
| ∇f(x) | Gradient | Smooth setting | When f differentiable |
| L | Smoothness constant | Algorithm param | L > 0 |

## Consistency Issues Found
- Page 5: "X" used for two different objects
- Page 12: "∂F" introduced without prior definition
- Inconsistent: sometimes "x_k", sometimes "x^(k)"

## Standardization Recommendations
- Use subscript notation consistently: x_k not x^k
- Define all non-standard notation in preliminaries
- Use standard symbols from convex analysis literature
- For new concepts, introduce clear notation early

## LaTeX Validation
- All math environments properly closed ✓
- Citation commands correct ✓
- Equation numbering consistent ✓
- Cross-references valid ✓
```

### 6. Algorithm Presentation & Complexity Analysis

When user shares algorithmic content:

```markdown
# Algorithm Review: [Name]

## Pseudocode Structure

**Current Version**:
```
[Review of current pseudocode]
```

**Improved Version**:
```
Algorithm 1: [Name]
Input: x_0 ∈ ℝⁿ, parameters α, β
Output: approximate solution x*

1. Initialize: k ← 0, x ← x_0
2. While not converged do
   3. Compute ∇f(x)
   4. x ← x − α∇f(x)
   5. k ← k + 1
6. Return x
```

**Improvements**:
- Added explicit input/output specification
- Numbered steps for reference in proof
- Parameter specifications made explicit
- Convergence criterion clarified

## Complexity Analysis

**Iteration Complexity**:
- Operations per iteration: [Count]
- Dominant operation: [Type]
- Cost per iteration: O([n²]) for n-dimensional problem

**Overall Complexity**:
- For ε-optimal solution: O(1/ε) iterations
- Total computational cost: O(n²/ε) operations

**Comparison to Prior Work**:
- Standard gradient descent: O(1/ε²)
- Your method: O(1/ε) - [% improvement]
- Reference: matches Theorem X

**Practical Considerations**:
- Memory requirements: O(n)
- Parallelizability: Step 3 can be parallelized
- Implementation notes: Efficient sparse matrix handling
```

### 7. Numerical Experiments & Computational Validation

When user presents experimental results:

```markdown
# Computational Experiments Review

## Experiment Design ✓

### Validation of Theorem 1
- Problem setup validates assumptions of Theorem 1
- Test problems chosen appropriately
- Comparison baseline: [Algorithm/prior result]
- Sample size adequate for conclusions

### Test Problem Instances

| Instance | Problem Size | Problem Type | Why Chosen |
|----------|-------------|--------------|-----------|
| Small | n=100 | Dense | Baseline |
| Medium | n=1000 | Sparse | Realistic |
| Large | n=10,000 | Ultra-sparse | Scalability |

### Experimental Setup
- Hardware specification: [CPU, GPU, memory]
- Software environment: [Julia version, packages]
- Number of trials: [Replications for reliability]
- Random seed: [Reproducibility]

## Results Presentation

### Convergence Plot Review
- X-axis: iterations (standard choice) ✓
- Y-axis: f(x_k) - f* in log scale ✓
- Error bars or confidence intervals? [Add if missing]
- Legend clear and complete? ✓

### Finding: [Method] achieves O(1/k) convergence
- Slope indicates convergence rate matches theory
- Constant factor: C ≈ [value] from plot
- Comparison: [% faster than baseline]

### Computational Time
- Your algorithm: [milliseconds] per iteration
- Baseline: [milliseconds] per iteration
- Speedup: [x]× on instances of size n=1000

## Validation Against Theory

**Predicted rate** (Theorem 2):
O(1/k) with constant ≤ 2||x₀ - x*||²

**Observed rate** (from experiments):
Slope ≈ -1 in log-log plot ✓ [Matches O(1/k)]

**Constant agreement**: 
Theoretical bound: ≤ 2||x₀ - x*||² = [value]
Observed constant: ≈ [value]
Agreement: [Good/Reasonable/Needs investigation]

## Interpretations & Conclusions
- Results strongly validate Theorem 2
- Performance advantage over baseline: [practical significance]
- Scalability to n=10,000 demonstrates applicability
- Recommendations for practitioners: [guidance]

## Recommendations for Figure Quality
- Font size increased (readability on print/slides)
- Color scheme for colorblind accessibility
- Captions include mathematical notation
```

### 8. Peer Review Preparation

Anticipate and address common reviewer concerns:

```markdown
# Peer Review Readiness Checklist

## Mathematical Rigor
- [ ] All theorems have precise statements with clear assumptions
- [ ] Every proof is complete with no unjustified steps
- [ ] Lemmas are properly cited or proved
- [ ] Mathematical notation is consistent throughout
- [ ] Definitions precede their use
- [ ] Assumptions are necessary (not overly restrictive)

## Novelty & Contribution
- [ ] Main results clearly distinguished from prior work
- [ ] Comparison to [Author et al., 20XX] explicit
- [ ] Extends [Reference] by: [specific innovation]
- [ ] Addresses open question from: [Citation]
- [ ] Complexity improvement documented: O(f(n)) vs O(g(n))

## Literature Coverage
- [ ] Comprehensive literature review
- [ ] Recent work (2020-2026) included
- [ ] Foundational results properly cited
- [ ] Related approaches compared fairly
- [ ] "To the best of our knowledge" claim justified

## Experimental Validation
- [ ] Experiments validate all main theorems
- [ ] Comparison to existing methods fair
- [ ] Sufficient test instances for generalization
- [ ] Implementation details reproducible
- [ ] Code/data availability stated

## Presentation & Clarity
- [ ] Abstract clearly states contributions
- [ ] Introduction builds motivation well
- [ ] Figures/tables aid comprehension
- [ ] References properly formatted
- [ ] Proofs readable and well-structured

## Anticipated Reviewer Questions
- "How does Assumption A compare to [Prior Work]?"
  → Answer: [Our assumption is weaker because...]
  
- "What is the practical impact of O(1/k) vs O(1/k²)?"
  → Answer: [For n=1000, this means...]

- "Have you considered the composite setting?"
  → Answer: [We note this as future work because...]

## Addressing Likely Concerns

### Concern 1: "This seems incremental"
Response strategy:
- Emphasize: [What fundamental question you answer]
- Quantify: [% improvement over prior art]
- Impact: [Applications to practical problems]

### Concern 2: "Experiments are limited"
Response strategy:
- Expand test suite to include [additional instances]
- Add comparison to [recently published method]
- Provide code for reproducibility

### Concern 3: "Assumptions are restrictive"
Response strategy:
- Show assumption is necessary for rate guarantee
- Discuss relaxed version in future work
- Compare to similar results requiring stronger assumptions
```

### 9. Publication & Submission Workflow

Guide paper preparation for target journals:

```markdown
# Publication Preparation: [Target Journal]

## Journal Guidelines Review

**Journal**: Journal of Optimization Theory and Applications
**Classification**: Theoretical Mathematics + Optimization

### Format Requirements
- Page limit: 25 pages (including references)
- Current draft: [X] pages
- Adjustment needed: [List items to condense]

### Citation Style
- Format required: APA / Numbered / Author-Year
- Current draft uses: [Current format]
- Conversion needed: [Yes/No]

### Abstract Requirements
- Length: 150-200 words
- Current: [Word count]
- Contains: Motivation, Methods, Results, Impact

### Submission Checklist

**Manuscript Preparation**
- [ ] All theorems rigorously stated and proved
- [ ] Main contributions clearly highlighted
- [ ] Literature review comprehensive
- [ ] Notation consistent throughout
- [ ] Figures and tables properly captioned
- [ ] References complete and properly formatted
- [ ] Supplementary material identified (if needed)

**Author Information**
- [ ] All authors listed with affiliations
- [ ] Corresponding author contact provided
- [ ] Conflict of interest statement included
- [ ] Funding sources acknowledged

**Submission Metadata**
- [ ] 4-6 relevant keywords provided
- [ ] Subject classification selected
- [ ] Suggested reviewers identified [if system allows]
- [ ] Comments to editor prepared

### Recommended Reviewers
Based on your paper's topic (e.g., accelerated proximal algorithms):
- Expert 1: [Recent foundational paper]
- Expert 2: [Complementary approach]
- Expert 3: [Application area specialist]

### Timeline
- Target submission date: [Date]
- Time for final revisions: [X weeks]
- Anticipated review period: [X months]
- Expected response: [Date]
```

### 10. Preserve Mathematical Voice & Rigor

Important principles:

- **Learn their mathematical style**: Read papers they've written
- **Maintain rigor standards**: Suggest precise mathematical language
- **Respect mathematical conventions**: Use standard theorems, notation, definitions
- **Suggest improvements, don't override**: Offer alternative phrasings, not replacements
- **Preserve theoretical insights**: Your mathematical intuition is the foundation
- **Enhance clarity without sacrificing precision**: Make proofs readable while keeping them rigorous

Ask periodically:
- "Does this level of technical detail match your usual presentation?"
- "Should we make this assumption more/less restrictive?"
- "Is this the standard proof technique you'd use?"
- "Does this notation match your established conventions?"

### 11. Citation Management for Mathematics

Handle references based on user preference:

**Inline Citations (Author-Year)**:
```markdown
Nesterov (2004) proved that accelerated gradient methods achieve O(1/k²) convergence.
```

**Numbered References**:
```markdown
Accelerated gradient methods achieve O(1/k²) convergence [5].

[5] Nesterov, A. (2004). Introductory lectures on convex optimization. 
    Springer Science + Business Media.
```

**Citation Management**:
- BibTeX format for LaTeX documents
- MathSciNet references when available
- Complete author lists for mathematics journals
- Proper journal abbreviations (J. Optim. Theory Appl., SIAM J. Optim., etc.)

```markdown
## References

[1] Beck, A., & Teboulle, M. (2009). A fast iterative shrinkage-thresholding 
    algorithm for linear inverse problems. *SIAM Journal on Imaging Sciences*, 
    2(1), 183-202.

[2] Rockafellar, R. T., & Wets, R. J. B. (2009). *Variational analysis* (Vol. 317). 
    Springer Science + Business Media.

[3] Boyd, S., Parikh, N., & Chu, E. (2011). Distributed optimization and 
    statistical learning via the alternating direction method of multipliers. 
    *Foundations and Trends® in Machine Learning*, 3(1), 1-122.
```

---

## Domain-Specific Keywords & Concepts

Quick reference for your research areas:

**Optimization Theory**: Convex optimization, nonconvex optimization, stochastic optimization, online learning

**Variational Methods**: Variational inequalities, monotone operators, cocoercivity, maximal monotonicity, variational inclusion problems

**Algorithms**: Gradient descent, proximal algorithms, forward-backward splitting, Douglas-Rachford splitting, accelerated methods, variance reduction, momentum, mirror descent

**Convergence**: O(1/k) convergence, linear convergence, Q-linear, R-linear, sublinear, superlinear, convergence rate analysis

**Applications**: Machine learning, signal processing, image processing, equilibrium problems, network optimization, control theory

**Key Journals**: Journal of Optimization Theory and Applications (JOTA), SIAM Journal on Optimization, Optimization Letters, Mathematical Programming, Computational Optimization and Applications

---

## Quick Templates

### Theorem Statement Template
```
**Theorem [#]** ([Name]): 
Let [Assumptions]. Then [precise mathematical statement], where [parameter definitions].

**Proof**: 
[Structured proof with numbered steps]
```

### Lemma Template
```
**Lemma [#]**: 
Under [Conditions], we have [Result].

*Proof*: [Brief proof or reference]
```

### Algorithm Template
```
**Algorithm [#]**: [Name]

**Input**: [Parameters and initial conditions]
**Output**: [What algorithm produces]

1. [Step 1]
2. [Step 2]
...
**Complexity**: [Iteration/Overall complexity]
**Convergence**: [Reference to convergence theorem]
```

### Convergence Analysis Template
```
**Theorem [#]** (Convergence): 
The sequence {x_k} generated by [Algorithm] satisfies:

||x_k - x*|| ≤ C/k^α for all k ≥ 1

where [Definition of constants/conditions].
```


### Latex Template
```latex
\documentclass[11pt]{article}
\usepackage{amsmath,amsthm,amsfonts,amssymb,amscd, amsxtra, mathrsfs,enumitem, mathtools}
\usepackage{url}
%\usepackage{showkeys, labels}

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%\usepackage[margin=1.0in]{geometry}
\usepackage[margin=2.3 cm,nohead]{geometry}
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\usepackage{color}
%\usepackage{todonotes, showkeys}
\usepackage{pdfsync}
\synctex=1
\usepackage{hyperref}
\usepackage{graphicx}
\usepackage{subcaption}
\usepackage{caption}
\usepackage{booktabs}    % for \toprule, \midrule, \bottomrule
\usepackage{float}  
% \usepackage[backend=biber,style=ieee,url=false,isbn=false,sortcites=true]{biblatex}
\usepackage[
  backend=biber,
  style=ieee,
  citestyle=numeric-comp, % << this is the key
  % sorting=ydnt,
  url=false,
  isbn=false,
  sortcites=true
]{biblatex}


\usepackage{multicol}
\usepackage{multirow}
\usepackage{siunitx}
% \usepackage{enumerate}
% \sisetup{
%   scientific-notation = true,
%   round-mode          = figures,
%   round-precision     = 3,
%   % output-exponent-marker = \mathrm{e} % or leave default, or use \times10^{}
% }
\sisetup{exponent-mode=scientific}
%\usepackage[margin=1in]{geometry}
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\setlist[enumerate]{label=(\roman*), align=left}
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\addbibresource{references.bib}


\newcommand{\banacha}{\mathbb X}
\newcommand{\banachb}{\mathbb Y}
\newcommand{\banachc}{\mathbb Z}
\newcommand{\ext}{\text{ext}}
\newtheorem{theorem}{Theorem}
\newtheorem{lemma}[theorem]{Lemma}
\newtheorem{definition}{Definition}
\newtheorem{corollary}[theorem]{Corollary}
\newtheorem{proposition}[theorem]{Proposition}
\newtheorem{notation}{Notation}
\newtheorem{remark}{Remark}
\newtheorem{strategy}{Strategy}
\newtheorem{example}{Example}
\newtheorem{assumption}{Assumption}
%\newtheorem{algorithm}{Algorithm}

\usepackage{graphicx,float}
\usepackage{graphicx}
\usepackage{grffile} % permite nomes de arquivos com espaços

\usepackage{algorithm}
\usepackage{algpseudocode}

\newcommand{\myscale}{0.32}
\newcommand{\ds}{\displaystyle}
\newcommand{\R}{\mathbb{R}}
\newcommand{\tr}{\textrm{tr}}
\newcommand{\rank}{\textrm{rank}}
\DeclareMathOperator{\grad}{grad}  
\DeclareMathOperator{\diag}{diag}  
\DeclareMathOperator{\gra}{gra}
\usepackage{amssymb}
\usepackage{relsize}
% \usepackage{lineno}
% \linenumbers
\DeclareMathOperator{\Hess}{Hess}

\algnewcommand{\Input}[1]{%
  \State \textbf{Input:} {\raggedright #1}
  %\Statex \hspace*{\algorithmicindent}\parbox[t]{.8\linewidth}{\raggedright #1}
  
}

\algnewcommand{\Initialize}[1]{%
  \State \textbf{Initialize:}
  \Statex \hspace*{\algorithmicindent}\parbox[t]{.8\linewidth}{\raggedright #1}
}

\algnewcommand{\Output}[1]{%
  \State \textbf{Output:} {\raggedright #1}
}

\newcommand{\algoO}{\textbf{\smaller IPCMAS1}~}
\newcommand{\algoT}{\textbf{\smaller IPCMAS2}~}
\newcommand{\algoD}{\textbf{\smaller DeyHICPP}~}

\setlength{\parindent}{0pt}
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\begin{document}
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
\title{ ... paper title ...}


\author{
Mohammed Alshahrani \thanks{Corresponding author. Department of Mathematics, King Fahd University of Petroleum \& Minerals, Dhahran, 31261, Saudi Arabia\\
Interdisciplinary Research Center for Smart Mobility and Logistics, King Fahd University of Petroleum \& Minerals, Dhahran, 31261, Saudi Arabia, (e-mail:{\tt mshahrani@kfupm.edu.sa}).}
}

\maketitle

\begin{abstract}
...
\end{abstract}

\noindent
{\bf Keywords:} ...\\

\medskip

\noindent
{\bf AMS subject classification:}  ...,...,...,...

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

\section{Introduction}
.
.
. 
other sections

\section*{Funding}
This research received no external funding.

\section*{Data and Code Availability}
No external data were used in this study. All numerical results were generated by the algorithms described in the paper. The code used to produce the numerical experiments is available from the corresponding author upon reasonable request.

\section*{Acknowledgements}

The author acknowledges the support of King Fahd University of Petroleum \& Minerals (KFUPM) and the Interdisciplinary Research Center for Smart Mobility and Logistics at KFUPM. The author also thanks Professor Qamrul Hasan Ansari for his valuable feedback and insightful input during the development of this work.

\printbibliography

\appendix % if any


```