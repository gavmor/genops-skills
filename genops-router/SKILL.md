---
name: genops-router
description: "Route experiments to GenOps skill based on stage."
---

# GenOps Stage Router

Use this meta-skill to determine which GenOps sub-skill to load based on your experiment's current stage and what you're about to do.

## Stage → Recommended Skills

- **Hypothesis Backlog** (no active skill yet)
  - Experiments here are queued but not yet resourced
  - Wait for steering committee prioritization
  - No GenOps skill needed yet

- **Experimental Design**
  - *Load*: `genops-experiment-init` (create branch, EXPERIMENT.yml scaffold)
  - *Load*: `genops-a3-report` (start A3 stub with problem statement + hypothesis)
  - *Then*: Write protocol, resource design, acquire materials

- **Active Testing**
  - *Load*: `genops-kanban` (move stage, check WIP limits)
  - *Load*: `genops-a3-report` (build Design section, Results placeholders)
  - *Then*: Execute experiments, collect telemetry, annotate anomalies

- **Data Analysis**
  - *Load*: `genops-a3-report` (fill Results + Data Viz section)
  - *Load*: `genops-definition-of-done` (verify Statistical Analysis item)
  - *Then*: Compute confidence intervals, p-values, derive insights

- **Peer Review**
  - *Load*: `genops-definition-of-done` (full 6-point checklist)
  - *Load*: `genops-a3-report` (finalize Conclusion + Next Steps)
  - *Then*: Independent peer review, address feedback, iterate

- **Promotion Decision**
  - *Load*: `genops-readiness-gates` (assess TRL/IRL/SRL)
  - *Load*: `genops-readiness-gates` (run regression testing suite)
  - *Then*: Steering committee go/hold/recycle/kill decision

- **Quarterly Portfolio Review**
  - *Load*: `genops-kill-matrix` (score stalled/failing experiments)
  - *Then*: Independent Kill Committee reviews; go/recycle/kill decision

## Quick Decision Tree

```
Question: What are you about to do?

├─ Start a new experiment?
│  └─> Load genops-experiment-init
│
├─ Move an experiment to the next stage?
│  └─> Load genops-kanban
│
├─ Build or iterate A3 report?
│  └─> Load genops-a3-report
│
├─ Verify experiment is complete (ready for review)?
│  └─> Load genops-definition-of-done
│
├─ Assess readiness for mainline promotion (TRL/IRL)?
│  └─> Load genops-readiness-gates
│
└─ Decide whether to kill a stalled experiment?
   └─> Load genops-kill-matrix
```

## Key Principle: Progressive Disclosure

Do not load all GenOps skills at once. Load only what's relevant to your current task. This keeps the context focused and prevents skill descriptions from conflicting.

## Anti-Rationalization

- **"I'll use the skill later when I'm done."** → Use it *during* each stage. Skills encode process; using them after the fact is like writing tests after deployment—it doesn't work.
- **"My experiment doesn't fit neatly into one stage."** → Experiments can be in multiple stages simultaneously *if* tracking is explicit. Use `EXPERIMENT.yml:kanban_stage` and `EXPERIMENT.yml:notes` to document the reason (e.g., "Active Testing ongoing; early Data Analysis parallel-path"). Don't hide multi-stage states.
