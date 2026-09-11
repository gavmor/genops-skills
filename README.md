# Generative Operations Protocol (GenOps) Skills

A modular skill ecosystem for orchestrating high-throughput R&D experiments via Kanban flow, Definition of Done, A3 reports, readiness gates, and portfolio rationalization.

## Overview

GenOps combines:
- **Kanban visual management** – Track experiments across defined lifecycle stages
- **Agile-Stage-Gate hybrid** – Iterative execution with formal promotion gates
- **Definition of Done (DoD)** – Standardized completion checklist across all experiments
- **A3 visual abstracts** – One-page problem-solving "mini posters" built iteratively
- **Technology/Integration Readiness Levels (TRL/IRL/SRL)** – Objective maturity assessment before mainline promotion
- **Sunk Cost Kill Matrix** – Forward-looking portfolio rationalization (ignore past investment; focus on future ROI)

Built on principles from:
- Lean/Kanban (visual management, WIP limits)
- Agile-Stage-Gate hybrid (Cooper, Stage-Gate methodology)
- A3 problem-solving (Toyota)
- Technology Readiness Levels (NASA/aerospace)
- Behavioral economics (sunk cost fallacy)
- Agent Skills design principles (Addy Osmani)

## Skills Included

### Core Skills

| Skill | Purpose |
|-------|---------|
| **`genops-framework`** | Conceptual overview, principles, architecture |
| **`genops-router`** | Route to correct sub-skill based on experiment stage |
| **`genops-experiment-init`** | Scaffold new experiment: branch, EXPERIMENT.yml, A3 stub |
| **`genops-kanban`** | Manage board, enforce WIP limits, compute flow metrics |
| **`genops-definition-of-done`** | Verify experiment completion before gate review |
| **`genops-a3-report`** | Build iterative visual abstracts (PDCA format) |
| **`genops-readiness-gates`** | Assess TRL/IRL/SRL maturity before promotion |
| **`genops-kill-matrix`** | Score stalled experiments for portfolio rationalization |

### Design Principles

Each skill follows **five load-bearing design principles**:

1. **Process over prose** – Workflows with checkpoints, not essays
2. **Anti-rationalization tables** – Pre-written rebuttals to common excuses
3. **Verification is non-negotiable** – Every workflow ends in concrete evidence
4. **Progressive disclosure** – Load only relevant skills per task; activate the router
5. **Scope discipline** – Touch only what you're asked to touch

## Repository Architecture

GenOps expects two git repositories:

### Main Repository (Experiments)
- Contains experiment branches: `feature/*`, `fix/*`, `refactor/*`, `docs/*`
- Each branch carries `EXPERIMENT.yml` at root with metadata
- `EXPERIMENT.yml` conflicts on merge to `main` = signal of successful promotion
- Production workflows live on `main` only

### Wiki Repository (Tracking)
- Authoritative Kanban board (`KANBAN.md`)
- A3 reports (`A3/<experiment-id>.md`)
- Readiness assessments (`assessments/<experiment-id>.yml`)
- Kill matrix scores (`kill-matrix/<date>.json`)
- Single source of truth for experiment lifecycle visibility

## Quick Start

### 1. Install in Claude Code

```bash
# Add to your Hermes profile
hermes skills import genops-framework genops-router genops-experiment-init genops-kanban genops-definition-of-done genops-a3-report genops-readiness-gates genops-kill-matrix
```

### 2. Initialize Experiment Tracking

```bash
# Clone your main repo and wiki repo
git clone <your-experiments-repo> my-experiments
git clone <your-experiments-repo>.wiki my-experiments.wiki

# Use genops-kanban to initialize KANBAN.md
# Use genops-experiment-init to start first experiment
```

### 3. Workflow: New Experiment

```
Use genops-router → Route to genops-experiment-init
→ Create branch, scaffold EXPERIMENT.yml, A3 stub
→ During design: use genops-a3-report to build sections
→ During active testing: use genops-kanban to move stage
→ Before peer review: use genops-definition-of-done checklist
→ Before promotion: use genops-readiness-gates for TRL/IRL assessment
→ Quarterly: use genops-kill-matrix to score stalled experiments
```

## EXPERIMENT.yml Schema

Each experiment branch carries:

```yaml
---
experiment_id: "feature-h3-consistency-test"
lead_researcher: "Alice Chen"
start_date: "2026-09-01"
target_completion: "2026-10-15"

hypothesis: "Seed-controlled prompt generation reduces output variance by >20%"
problem_statement: "Current h3 workflows show high variance; reproducibility unclear"
strategic_alignment: "Tier-1: Core product reliability"

primary_artifacts:
  - "workflows/h3-seed-consistency.api.json"
  - "analysis/seed-variance.py"

analysis_scripts:
  - "analysis/compute_variance.py"
  - "analysis/plot_results.py"

kanban_stage: "Active Testing"
kanban_stage_entered: "2026-09-05"

a3_report: "A3/feature-h3-consistency-test.md"
readiness_assessment: "assessments/feature-h3-consistency-test.yml"

notes: "Delayed by hardware allocation; resumed 2026-09-04"
```

## Key Principles

### No Zombie Projects
WIP limits force completion. Kill matrix terminates unviable work.

### Single Source of Truth
Two repos only: main (experiments) + wiki (tracking). No external databases.

### Objective Gates
All decisions data-driven: DoD checklist, TRL/IRL/SRL scores, Kill Matrix composites.

### Visual Management
Kanban board + A3 reports make status immediately obvious at a glance.

### No Career Penalty
Kill decisions are operational milestones, not discipline. Shift conversation from "sunk cost" (emotional, past) to "forward ROI" (objective, future).

## Common Anti-Rationalizations

Each skill includes rebuttals to common excuses. See skill files for full tables. Examples:

- *"This is too simple to need an A3."* → A3 is 70% done by Design phase; zero marginal cost.
- *"Tests pass; ship it."* → Tests are evidence, not proof. Verification is non-negotiable.
- *"We invested six months; we can't kill it."* → Past is irrelevant. What return does the *next dollar* generate?

## File Structure

```
genops-skills/
├── README.md (this file)
├── LICENSE
├── genops-framework/
│   └── SKILL.md
├── genops-router/
│   └── SKILL.md
├── genops-experiment-init/
│   └── SKILL.md
├── genops-kanban/
│   └── SKILL.md
├── genops-definition-of-done/
│   └── SKILL.md
├── genops-a3-report/
│   └── SKILL.md
├── genops-readiness-gates/
│   └── SKILL.md
└── genops-kill-matrix/
    └── SKILL.md
```

## License

MIT

## References

- Kanban: Toyota Production System, David Anderson (*Kanban: Successful Change Management*)
- Agile-Stage-Gate: Robert Cooper (*Stage-Gate Development Processes*)
- A3 Problem-Solving: Toyota, Art Smalley (*Creating Continuous Improvement Culture*)
- Technology Readiness Levels: NASA, U.S. Department of Defense (TRL 1-9 scale)
- Integration Readiness Levels: aerospace engineering standards
- Sunk Cost Fallacy: behavioral economics (Thaler, Arkes, Blumer)
- Agent Skills: Addy Osmani (*Agent Skills: Process over Prose*)

## Contributing

These skills are portable across AI coding agents (Claude Code, Cursor, Gemini CLI, Codex, etc.) and designed for team use. Feel free to fork, adapt, and contribute improvements.

## Questions?

See individual skill files for detailed workflows, templates, and examples.
