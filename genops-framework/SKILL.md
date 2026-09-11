---
name: genops-framework
description: "GenOps: Kanban + Definition of Done + readiness gates."
---

# Generative Operations Protocol (GenOps)

The GenOps framework combines Kanban flow, Agile-Stage-Gate hybrid governance, Definition of Done checklists, A3 visual abstracts, Readiness Levels (TRL/IRL/SRL), and Sunk Cost Kill Matrix to transform chaotic experimentation into high-throughput, objectively-governed R&D.

## Core Principles

- **No zombie projects** – WIP limits force completion; kill matrix terminates unviable work
- **Single source of truth** – Git branches + wiki repo only; no external databases
- **Objective gates** – Decisions data-driven (DoD, TRL/IRL, Kill Matrix), not opinion
- **Visual management** – Kanban board + A3 reports make status immediately obvious
- **Reproducibility** – Full experiment arc preserved; promoted workflows carry lineage
- **No career penalty** – Kill decisions are operational milestones, not discipline

## Repository Architecture

- **Main repo** (experiment branches with `EXPERIMENT.yml` at root)
- **Wiki repo** (authoritative Kanban board, A3 reports, readiness assessments, kill matrix scores)

## Experiment Lifecycle

1. **Hypothesis Backlog** – Unverified ideas, prioritized
2. **Experimental Design** – Protocol formulated, resources acquired
3. **Active Testing** – Execution phase, data collection
4. **Data Analysis** – Raw data → insights, statistical rigor
5. **Peer Review** – Defense, A3 finalization
6. **Promoted/Archived** – Terminal: integrated to mainline or killed

## Gate Decisions

At each stage boundary, steering committee chooses:

- **Go** – Meets stage-exit criteria; proceed
- **Hold** – Paused pending external resolution
- **Recycle** – Return to previous stage for rework
- **Kill** – Terminate, archive data, release resources

## Sub-Skills

- `genops-kanban` – Board management, WIP enforcement, flow metrics
- `genops-experiment-init` – Scaffold new experiment
- `genops-definition-of-done` – Completion verification
- `genops-a3-report` – Visual abstract generation
- `genops-readiness-gates` – TRL/IRL/SRL assessment
- `genops-kill-matrix` – Portfolio rationalization

## EXPERIMENT.yml Schema

Each experiment branch carries metadata:
- experiment_id, lead_researcher, start/target dates
- hypothesis, problem_statement, strategic_alignment
- workflows (primary artifacts), analysis_scripts
- kanban_stage (current phase)
- Links to A3, readiness assessment, kill-matrix score

On merge to `main`, `EXPERIMENT.yml` conflicts, signaling promotion success.

## WIP Limits & Flow Control

Typical limits: 3-5 per stage (adjust for team). Hard constraints:
- If stage is full, no new experiments enter until one exits
- Forcing function: when limit hits, team swarms bottleneck

## Flow Metrics (Auto-Computed)

- **Cycle Time** – Duration from "Active Testing" start to "Done"
- **Lead Time** – Duration from backlog entry to final resolution
- **Throughput** – Experiments completed per sprint/quarter
- **Queue/Wait Time** – Time in buffer states

Deviation >20% from baseline triggers management escalation.

## Definition of Done (DoD)

Universal checklist across all experiments:

1. **Execution Completeness** – All planned iterations complete, controls run
2. **Data Integrity** – Raw data archived with metadata, schemas applied
3. **Statistical Analysis** – Confidence intervals, p-values, outliers annotated
4. **Reproducibility** – SOP codified, dependencies pinned, replication script included
5. **Definitive Synthesis** – Clear determination: hypothesis supported/partial/falsified
6. **Visual Documentation** – A3 complete, proper hierarchy, accessible colors, 20-30% white space

## A3 Report: Visual Abstract

Stored at `A3/<experiment-id>.md` in wiki repo. Follows PDCA flow:

- **Title & Metadata** (top header)
- **Problem Statement** (top left)
- **Current Condition** (middle left)
- **Root Cause / Hypothesis** (bottom left)
- **Experimental Design** (top right)
- **Results & Data Visualization** (middle right)
- **Conclusion & Next Steps** (bottom right)

Built iteratively as experiment progresses, not as final burden.

## Readiness Levels: TRL, IRL, SRL

**TRL (Technology Readiness Level):** Is the specimen mature in isolation? (1-9 scale)
- TRL 1-3: Exploratory (theory, POC)
- TRL 4-6: Development (component validation)
- TRL 7-9: Deployment (proven in operations)

**IRL (Integration Readiness Level):** Does it integrate cleanly with mainline? (0-9 scale)
- IRL 0-2: Isolated (standalone)
- IRL 3-5: Compatible (shares resources, no modification)
- IRL 6-9: Integrated (replaces/extends mainline)

**SRL (System Readiness Level):** Holistic maturity = min(TRL, IRL)

Promotion gate requires TRL ≥7 + IRL ≥6 (SRL ≥6).

## Sunk Cost Kill Matrix: Portfolio Rationalization

Quarterly review: score each experiment on five forward-looking dimensions (0-10):

1. **Forward ROI Ratio** – Expected return if we invest $X more?
2. **Technical Velocity** – Real progress in last 30 days, or stalled?
3. **Market/Scientific Validation** – External data support or contradict hypothesis?
4. **Opportunity Cost** – What high-yield work is starved of resources?
5. **Strategic Alignment** – Does this still align with org goals?

**Composite Score** = mean of five dimensions
**Kill Threshold** = 3.5 (configurable; experiments below = automatic termination)

**Key principle:** Ignore sunk costs entirely. Shift conversation from "How much have we invested?" (past-focused, emotional) to "What return does the next dollar generate?" (forward-focused, objective).

Independent Kill Committee (zero budget stake in projects) scores, communicates decision, reallocates resources immediately. No career penalty for failure (kill decision is operational milestone, not discipline).

## Getting Started

1. Clone both repos
2. Use `genops-kanban` to initialize `KANBAN.md` in wiki
3. Use `genops-experiment-init` to scaffold new experiment
4. During active testing, update Kanban board as stages progress
5. Use `genops-definition-of-done` to verify completion
6. Use `genops-readiness-gates` for TRL/IRL assessment before promotion
7. Quarterly: run `genops-kill-matrix` on stalled experiments

## Scope Discipline: Touch Only What You're Asked to Touch

An experiment is scoped to one hypothesis and one set of workflows. When implementing:

- **Do**: Commit changes to `EXPERIMENT.yml` (metadata), your target workflows, and your analysis scripts
- **Do**: Update the Kanban board (move stage) when appropriate
- **Do**: Modify shared utilities (e.g., label-render.sh) *only* if the change is backward-compatible and doesn't break other experiments
- **Don't**: Refactor unrelated production workflows
- **Don't**: Modify `EXPERIMENT.yml` on branches other than your own
- **Don't**: Delete or remove workflows you don't fully understand (Chesterton's Fence)
- **Don't**: Brush against a TODO in a shared workflow and decide to rewrite it
- **Don't**: Change WIP limits or gate thresholds without steering committee approval

**Scope violations make PRs unmerge-able. Stick to your hypothesis.**

## References

Kanban (Lean), Agile-Stage-Gate hybrid (Cooper), A3 problem-solving (Toyota), TRL/IRL (NASA/aerospace), Sunk Cost Kill Matrix (behavioral economics), Agent Skills design principles (Addy Osmani).
