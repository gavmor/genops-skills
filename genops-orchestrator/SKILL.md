---
name: genops-orchestrator
description: "Chain all skills into autonomous overnight loop."
---

# GenOps Orchestrator: Autonomous Experiment Loop

Use this meta-skill to understand how all skills chain together into an **autonomous overnight experiment execution system**. This is the conceptual glue.

## The Full Loop (Automated End-to-End)

```
User Input (You send a link)
    ↓
[genops-experiment-runner]
├─ Extract metadata from link
├─ Create feature branch
├─ Scaffold EXPERIMENT.yml + Concourse pipeline
├─ Push to GitHub
└─ Concourse auto-runs instance pipeline
    ↓
[Overnight: Concourse executes pipeline]
├─ Download model/workflow
├─ Submit to ComfyUI
├─ Poll for completion (4-24 hours)
├─ Extract metrics to Concourse artifacts
└─ Job completes; outputs in R2
    ↓
[genops-results-parser] (auto, after pipeline completes)
├─ Parse Concourse logs → extract latency, VRAM, queue time
├─ Compare to baseline
├─ Update EXPERIMENT.yml:results section
└─ Write metrics.json for downstream
    ↓
[genops-a3-auto-renderer] (auto, after results-parser)
├─ Read EXPERIMENT.yml + metrics.json
├─ Render A3 markdown (PDCA format)
├─ Commit to wiki repo
├─ Update EXPERIMENT.yml:a3_report link
└─ Move Kanban stage to "Peer Review"
    ↓
[Cron: 08:00 UTC] genops-morning-digest
├─ Poll all experiments (git + Concourse)
├─ Collect A3 reports + results
├─ Format human-readable digest
└─ Post to Slack/email with decision checkboxes
    ↓
You read digest (5 min) and reply with decisions
    ↓
[genops-orchestrator] executes your decisions
├─ Merge branches to main ("Merge & Promote")
├─ Delete branches ("Kill & Archive")
├─ Create recycled branches with params ("Recycle & Retry")
└─ Update Kanban board accordingly
    ↓
Loop repeats next night
```

## Timing Breakdown

| Stage | Duration | Ownership | Next Stage |
|-------|----------|-----------|------------|
| User submits link | 1 sec | Manual | experiment-runner |
| experiment-runner scaffolds | 2 min | Automated | Concourse pipeline |
| Concourse executes | 4-24 hrs | Concourse | results-parser |
| results-parser extracts metrics | 2 min | Automated | a3-auto-renderer |
| a3-auto-renderer renders | 1 min | Automated | morning-digest |
| **You review digest** | **5 min** | **Manual** | orchestrator |
| orchestrator executes decisions | 5 min | Automated | Main branch or recycle |
| **Total**: Overnight + 5 min human time | | | |

## Key Principles

### 1. No Human Friction Until You Decide

- Experiment runs unattended
- Results parsed automatically
- A3 rendered automatically
- You see polished summary in morning
- You decide: 3-5 options (merge/kill/recycle/hold/further-testing)
- All execution automated from your decision

### 2. Parallelism

- Multiple experiments can run simultaneously (Concourse instance pipelines)
- GPU lock ensures only one has GPU at a time (fair queuing)
- Morning digest batches all decisions into one review cycle

### 3. Idempotency

- Parsing, A3 rendering, digest generation are deterministic
- Re-running the same experiment = same result (assuming deterministic ComfyUI)
- No hidden state; everything tracked in git

### 4. Reversibility

- Every decision (merge, kill, recycle) is a git operation
- Undo: `git revert <commit>` or restore branch from backup
- A3 reports and results preserved in wiki repo forever

## When to Load Each Skill

| When | Load |
|------|------|
| User sends you a link | Load `genops-experiment-runner` |
| You want to manually run results pipeline | Load `genops-results-parser` + `genops-a3-auto-renderer` |
| You want to understand overnight flow | Load `genops-orchestrator` (this skill) |
| Every morning at 08:00 | Cron auto-runs `genops-morning-digest` |
| After morning review | Load `genops-orchestrator` to execute decisions |
| Quarterly portfolio review | Load `genops-kill-matrix` |

## Cron Configuration

```yaml
jobs:
  - name: genops-nightly
    schedule: "0 22 * * *"  # 22:00 UTC (start night run window)
    prompt: |
      Poll all experiment branches. For any in "Active Testing" 
      without results, check Concourse status. If not running, 
      kick off pipeline via fly trigger-job.
    
  - name: genops-morning-digest
    schedule: "0 8 * * *"   # 08:00 UTC (morning review)
    prompt: |
      Load genops-morning-digest. Summarize overnight results.
      Post digest with decision checkboxes to Slack.
    attach_to_session: true  # Wait for your reply with decisions
    
  - name: genops-execute-decisions
    schedule: "on-demand"     # Triggered after you reply
    prompt: |
      Load genops-orchestrator. Parse your decisions from morning digest.
      Execute merges, kills, and recycles.
```

## Decision Format (Morning Digest Reply)

When you receive the morning digest, reply with checkboxes:

```
test-qwen-vl-20260910: ☑️ Yes (merge)
test-llava-multimodal-20260910: ☑️ Recycle (with params: seed=42,steps=50)
test-stable-diffusion-xl-20260910: ☑️ Kill
fix-label-render-edge-case-20260910: ☑️ Yes (merge)
feature-prompt-optimization-20260910: ☑️ Yes (merge)
test-mistral-quantization-20260910: ☑️ Yes (retry with --bits=8)
```

**Format Strict** (for parsing):
- `<experiment-id>: ☑️ <Action> (<optional params>)`
- Actions: `Yes` (merge), `Kill` (archive), `Recycle` (retry), `Hold` (defer)

## Anti-Rationalizations

- **"Why automate if I have to review every morning?"** → Automation handles the boring part (parsing, rendering, summarization). Your time is decision-making, not log-hunting.
- **"Should I just auto-promote winners?"** → No. Human judgment irreducible: quality inspection, strategic fit, integration risk all require judgment.
- **"What if I'm on vacation?"** → Experiments queue on GPU lock. Nothing runs until you review and decide. Safe backlog.
- **"Can I batch multiple links into one experiment?"** → No. One link = one experiment. Parallel is good; batching hides signal.

## Safety Guardrails

- **No auto-merge to main**: Requires explicit human decision
- **No auto-kill without A3**: Decision must be documented
- **No duplicate runs**: Check git for existing test-<model-id> branch before creating new
- **GPU exclusivity**: Concourse pool lock ensures no VRAM collision
- **Rollback always possible**: All decisions are git operations; revert if needed

## Notes

- This is a **high-throughput experimental rig**, not a production pipeline
- Expected failure rate ~20% (CUDA OOM, dependency issues, etc.) is normal
- Most experiments run to completion; ~5-10 per night is typical
- Parallel capacity bounded by GPU (single GPU = 1 at a time) and researcher attention (max ~10-15 results to review per morning)
