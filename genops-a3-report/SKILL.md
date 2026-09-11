---
name: genops-a3-report
description: "A3 visual abstract: create, build iteratively through."
---

# A3 Report: Visual Abstract Generation

Use this skill to create and iteratively update the A3 "mini poster" summarizing an experiment. Built continuously, not as final burden.

## A3 Anatomy: PDCA Flow

Top-left → bottom-right:

1. **Title & Metadata** (top header) – Experiment ID, lead, dates, stage
2. **Problem Statement** (top left) – Gap/issue being addressed
3. **Current Condition** (middle left) – Baseline metrics, prior art
4. **Root Cause / Hypothesis** (bottom left) – Mechanism, expected outcome
5. **Experimental Design** (top right) – Methodology, variables, controls
6. **Results & Data Visualization** (middle right) – Charts, stats, key findings
7. **Conclusion & Next Steps** (bottom right) – Hypothesis outcome, promotion path

## Markdown Template

Stored at `A3/<experiment-id>.md` in wiki repo:

```markdown
# A3: [Experiment Title]

**Experiment ID:** `feature/your-experiment-id`  
**Lead Researcher:** [Name]  
**Start Date:** YYYY-MM-DD  
**Last Updated:** YYYY-MM-DD  
**Current Stage:** [Active Testing / Data Analysis / Peer Review / Promoted]  

---

## Problem Statement

(2-3 sentences. What gap/issue? Business/scientific impact?)

---

## Current Condition

(Baseline metrics, prior art, state-of-the-art before intervention)

---

## Root Cause / Hypothesis

(Mechanism of improvement, expected outcome, theoretical justification)

---

## Experimental Design

(Methodology, variables, controls, success criteria, sample sizes)

---

## Results & Data Visualization

(Charts, tables, statistical summaries. Show uncertainty bands, not just point estimates.)

---

## Conclusion & Next Steps

(Hypothesis outcome: supported/partial/falsified. Implications for mainline. Next action.)
```

## Visual Design Guidelines

A3 is a single-page visual abstract:

- **Visual Hierarchy:** Critical finding is largest element. Problem/hypothesis secondary. Data tables tertiary.
- **Typography:** Sans-serif only (Arial, Helvetica). Max 3 font sizes (header ~24pt, subheader ~16pt, body ~12pt).
- **Color:** 2-4 colors max. Accessible for colorblindness (use ColorBrewer or high luminance contrast).
- **White Space:** Preserve 20-30% empty space. Margins ≥0.5" all sides.
- **Charts:** Show uncertainty (confidence bands, error bars). Label axes with units. Include N and p-values.

## Building Iteratively

Don't treat A3 as final writeup. Update as experiment progresses:

1. **Experimental Design stage:** Fill Problem Statement, Current Condition, Hypothesis, Design. Already 70% done before testing starts.
2. **Active Testing:** As data comes in, sketch preliminary results. Update Results section incrementally.
3. **Data Analysis:** Finalize stats and charts. Complete Conclusion.
4. **Peer Review:** Polish visuals, check accessibility, verify citations. A3 is now gate-review ready.

By Peer Review, A3 is finished—no last-minute scramble.

## Common Rationalizations (and Why They Don't Work)

- **"This experiment is too simple to need an A3."** → A3 is 70% done by Design phase; zero marginal cost. If you can't fill one page with findings, the experiment wasn't worth running.
- **"We can write the A3 after promotion."** → No. A3 is a gate review requirement. Findings without an abstract are claims, not proof. Write iteratively or it won't exist when needed.
- **"The charts are self-explanatory; skip the prose."** → Visualization alone is ambiguous. Prose disambiguates: what do the error bars mean? Why was this sample excluded? What does "similar" actually mean?
- **"Visual hierarchy is nice-to-have."** → Visual hierarchy is how reviewers scan in 30 seconds. No hierarchy = no review = no promotion. It's non-negotiable.
