---
name: genops-kill-matrix
description: "Score experiments: ROI, velocity, validation, opportunity."
---

# Sunk Cost Kill Matrix: Portfolio Rationalization

Objectively evaluate whether stalled/failing experiments should terminate. Ignores past investment (sunk costs), focuses on forward-looking metrics only.

## Why Experiments Get Stuck: Escalation of Commitment

Teams invest time, capital, reputation. Negative feedback causes cognitive dissonance. Subconsciously, they justify continued investment to avoid admitting prior waste. This is **escalation of commitment** (behavioral economics, well-documented).

**Kill Matrix antidote:** Shift from "What have we invested?" (past, emotional) to "What return does next dollar generate?" (future, objective).

## Kill Matrix: Five Scoring Dimensions

Score each experiment 0-10. Composite score = mean of five.

### 1. Forward ROI Ratio (0-10)

**Question:** If we invest $X more, what's expected return?

- **0-2:** Return minimal even if successful
- **3-4:** Modest return (e.g., 10-15% speedup at 6-month cost)
- **5-6:** Reasonable return but high risk (e.g., $1M if successful, 50% chance)
- **7-8:** Likely positive with manageable risk (e.g., $5M benefit, high confidence)
- **9-10:** Exceptional return, low risk (e.g., $20M+ unlocked, clear path)

### 2. Technical Velocity (0-10)

**Question:** Real progress last 30 days, or stalled?

- **0-2:** No commits 30+ days, or changes reverted
- **3-4:** Sporadic commits, progress unclear
- **5-6:** Steady but below baseline rate
- **7-8:** Consistent, baseline-rate progress
- **9-10:** Rapid iteration, clear direction, obstacles resolved fast

**Assess:** `git log --oneline <branch> --since="30 days ago" | wc -l`, review commit messages, check if `kanban_stage` advancing.

### 3. Market/Scientific Validation (0-10)

**Question:** External evidence support or contradict hypothesis?

- **0-2:** Recent external data heavily contradicts
- **3-4:** Partially contradicted (mixed signals)
- **5-6:** Neutral (no major external signals)
- **7-8:** Partially supports
- **9-10:** Strongly supports (recent external evidence confirms)

**Assess:** arXiv/peer-reviewed literature, competitor announcements, user feedback, market data.

### 4. Opportunity Cost (0-10)

**Question:** What high-yield experiments are starved of resources?

- **0-2:** Critical resources blocked; high-ROI work waiting
- **3-4:** Significant resources; better-ROI backlog exists
- **5-6:** Moderate contention
- **7-8:** Resources available
- **9-10:** Resources abundant, no bottlenecks

**Assess:** What would freed resources work on next? Is that higher-value?

### 5. Strategic Alignment (0-10)

**Question:** Still aligned with org goals? Have priorities shifted?

- **0-2:** Severely misaligned (priorities shifted)
- **3-4:** Secondary goal
- **5-6:** Contributes to goals but not central
- **7-8:** Clear connection to current goals
- **9-10:** Essential for achieving strategic objectives

**Assess:** Compare against current roadmap/OKRs. Has strategy shifted since inception?

## Kill Matrix Scoring Template

Store at `kill-matrix/<experiment-id>.yml` in wiki:

```yaml
---
experiment_id: feature/h3-30s-attention-stack-test
evaluation_date: 2026-08-20
evaluated_by: Independent Kill Committee (Dave, Eve, Frank)

scores:
  forward_roi_ratio: 2
  technical_velocity: 1
  market_validation: 4
  opportunity_cost: 2
  strategic_alignment: 5

forward_roi_ratio:
  score: 2
  rationale: |
    Expected benefit if successful: <$200K (small market window).
    Remaining investment: $150K (1 FTE, 6 months).
    ROI = $200K / $150K = 1.3x. Below minimum (2x breakeven).

technical_velocity:
  score: 1
  rationale: |
    23 days active; only 3 commits (avg 1/week; baseline 3-5/week).
    Last commit 7 days ago ("WIP: debugging").
    Core blocker unresolved; no clear path.

market_validation:
  score: 4
  rationale: |
    User survey (July): 12% rate "speed" critical (down from 23% Q1).
    Competitor claimed "40s by Q4" (closes window).
    Published paper: similar approach achieves 25s (different method).
    External validation weak.

opportunity_cost:
  score: 2
  rationale: |
    Senior engineer (Alice) 80% allocated.
    Blocked: feature/krea2-distill (ROI $5M+).
    GPU: 1x A100 full-time.
    Backlog: 2 high-ROI experiments (>$2M each) waiting.

strategic_alignment:
  score: 5
  rationale: |
    Q3 OKR: "<250s latency" (this helps).
    Q4 OKR (new): "Real-time mode" (this is potential, not critical).
    New priority: "Low-end hardware" (tangential).
    Alignment shifted.

composite_score: 2.8 / 10
kill_threshold: 3.5  # Below = automatic termination

decision: KILL
rationale: Score 2.8/10 (below 3.5). Five blockers: low ROI, stalled velocity, weak market validation, severe opportunity cost, shifted alignment.

approved_by: Independent Kill Committee
approval_date: 2026-08-20
post_kill_actions:
  - Update Kanban: move to "Killed"
  - Notify Alice: reassign to krea2-distill immediately
  - Update backlog: freed GPU unblocks waiting experiments
  - Archive data: wiki experiments/feature-h3-30s-attention-stack/
```

## Quarterly Portfolio Review Workflow

1. **Identify candidates:** Any in Active Testing/Data Analysis >60 days, OR concerning velocity
2. **Convene Independent Kill Committee:** Cross-functional, zero personal stake in projects
3. **Score:** Use five dimensions, be objective
4. **Composite:** Mean of five scores
5. **Decision:** Below kill_threshold = automatic Kill
6. **Communicate:** Lead researcher informed, immediately reallocated (no career penalty)
7. **Archive:** All data preserved in wiki for future reference

## Key Principles

- **Kill ≠ Discipline** – Terminating unviable experiment is operational success, not failure
- **Forward metrics only** – Ignore sunk costs entirely; past is irrelevant
- **Independent committee** – Zero budget stake in projects under review
- **Objective scoring** – Use five dimensions; avoid opinion
- **Automatic termination** – Below threshold = no exceptions
- **Rapid reallocation** – Freed resources immediately redirect to highest-ROI backlog

## Minimizing False Kills: Appeals Process

Rarely, score below threshold due to temporary setback (hardware failure, sick leave), not fundamental viability.

**Appeal window:** 48 hours for lead researcher to submit evidence
**Appeal criteria:** Removing temporary factor significantly improves score(s)
**Outcome:** Independent Committee holds (pause) or confirms Kill

Without appeals, transient problems could kill otherwise-viable work unfairly.

## Common Rationalizations (and Why They Don't Work)

- **"We just invested six months; we can't kill it now."** → That's the sunk cost fallacy. The past is irrelevant. Forward ROI is all that matters. Ignore the six months; what return does the next dollar generate?
- **"Technical velocity is low because we're unlucky with hardware."** → Temporary setbacks (hardware failures, sick leave) have an appeals process. Use it. But "unlucky" for 60 days isn't temporary.
- **"The market will validate this if we just ship it."** → Maybe. But the kill decision is made before shipping, based on *available* market data, not speculative data.
- **"Strategic alignment will shift; we just have to wait."** → Don't bank on hope. Judge alignment based on current strategy. If strategy changes, the experiment gets re-scored.
- **"Killing this experiment will damage team morale."** → Killing unviable work is a signal that the org is serious about ROI. Keeping zombie projects kills morale much faster.
- **"Independent Kill Committee has a conflict of interest."** → That's why it's *independent*. Zero budget stake, zero personal investment. Challenge the score, not the committee.
