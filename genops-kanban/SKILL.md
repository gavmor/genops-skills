---
name: genops-kanban
description: "Kanban board: initialize, move experiments, WIP, metrics."
---

# Kanban Board Management

Use this skill to create and maintain the Kanban board, enforce WIP limits, move experiments between stages, compute flow metrics.

## Board Structure

The authoritative Kanban board lives in wiki repo as `KANBAN.md`. One section per stage:

- **Hypothesis Backlog** (WIP: ∞) – Ideas awaiting design review
- **Experimental Design** (WIP: 3) – Protocols formulated, resources acquired
- **Active Testing** (WIP: 4) – Workflows executing, data being collected
- **Data Analysis** (WIP: 2) – Raw data synthesized into insights
- **Peer Review** (WIP: 2) – Findings defended, A3 finalized
- **Promoted/Archived** – Terminal state: integrated to mainline or killed

(Adjust WIP limits based on team size.)

## How to Update the Board

### Move an Experiment to a New Stage

1. Update `EXPERIMENT.yml` on the experiment's branch:
   ```yaml
   kanban_stage: "Data Analysis"  # Changed from "Active Testing"
   data_analysis_start: "2026-09-08"
   ```

2. Commit and push:
   ```bash
   git add EXPERIMENT.yml
   git commit -m "chore: move experiment to Data Analysis stage"
   git push
   ```

3. Regenerate the board:
   ```bash
   genops-kanban query --main-repo <path> --wiki-repo <path> > KANBAN.md
   git -C <wiki-repo> add KANBAN.md
   git -C <wiki-repo> commit -m "chore: update Kanban board (auto-generated)"
   git -C <wiki-repo> push
   ```

### Auto-Generate from Git

```bash
genops-kanban query \
  --main-repo ~/my-experiments \
  --wiki-repo ~/my-experiments.wiki \
  --wip-limits '{"design": 3, "active": 4, "analysis": 2, "review": 2}' \
  --output KANBAN.md
```

This:
- Clones/fetches all branches
- Extracts `EXPERIMENT.yml` from each
- Groups by `kanban_stage`
- Computes cycle/lead times, flow metrics
- Checks WIP violations
- Generates markdown board
- Optionally commits to wiki

## WIP Limit Enforcement

When a stage hits its limit, no new experiments enter until one exits. Hard constraint:

- **Prevention:** Board shows when limits exceeded; leadership must approve exception
- **Forcing function:** When triggered, team swarms bottleneck to unblock

## Flow Metrics Interpretation

- **Cycle Time** – Duration from "Active Testing" start to "Done". High = slow/blocked
- **Lead Time** – Duration from backlog entry to final resolution. Baseline for planning
- **Throughput** – Experiments completed per sprint. Measures velocity
- **Queue/Wait Time** – Time in paused states. Should be <10% of cycle time

Deviation >20% from baseline → escalate to management.

## Notes

- Board auto-generated from git; don't edit KANBAN.md manually
- Update source files (EXPERIMENT.yml) and regenerate
- Stage entry dates recorded in EXPERIMENT.yml; compute cycle time as (now - stage entry date)
- For "Hold" experiments, record reason and expected resolution in `notes` field

## Common Rationalizations (and Why They Don't Work)

- **"WIP limit doesn't apply to my experiment."** → WIP limits exist for a reason: prevent context switching and force completion. Exceptions require steering committee vote.
- **"I'll move the experiment to next stage when I have time."** → There is no "when I have time." Update `kanban_stage` in EXPERIMENT.yml immediately upon stage exit. The board reflects reality, or it's useless.
- **"Cycle time variance is normal; don't worry about it."** → Deviations >20% from baseline are escalation triggers. Ignore them and experiments disappear into limbo.
- **"The board is just for management; developers don't need to use it."** → The board is the shared source of truth. Every experiment update should be reflected in `kanban_stage`. If developers ignore it, visibility collapses.
