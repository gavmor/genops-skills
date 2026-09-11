---
name: genops-morning-digest
description: "Daily digest: overnight results, status, decisions."
---

# Morning Digest: Overnight Experiment Summary

Use this skill (typically as a scheduled cron job at 08:00) to poll all overnight experiments, summarize results, and present to you for decision-making.

## Input

- **All experiment branches** – Git metadata (started, completed, failed)
- **EXPERIMENT.yml per branch** – Kanban stage, results section
- **A3 reports** – Wiki repo `/A3/` directory
- **Concourse logs** – Build status, completion times

## Workflow

### 1. Poll All Overnight Experiments

```bash
# List all active feature/fix/refactor branches
git branch -r | grep -E 'origin/(feature|fix|refactor)/' | while read branch; do
  # Check if EXPERIMENT.yml exists and kanban_stage == "Active Testing"
  # If so, fetch EXPERIMENT.yml and check results section
  # If results section exists, mark as "completed"
  # If no results, check Concourse build status
done
```

Categorize into:
- ✅ **Completed** – Results parsed, A3 generated, ready for review
- ⏳ **In Progress** – Still running (show ETA)
- ❌ **Failed** – Pipeline errored (show error summary)
- 🟡 **Blocked** – Waiting for resources (e.g., GPU lock held)

### 2. Generate Summary Report

```markdown
# GenOps Morning Digest | 2026-09-11 08:00 UTC

**Overnight Summary**: 7 experiments started, 5 completed, 1 in progress, 1 failed

---

## ✅ Completed Experiments (Ready for Review)

### 1. test-qwen-vl-20260910
- **Hypothesis**: Qwen-VL reduces inference latency by >40%
- **Result**: ✅ **Supported** – 61.9% faster, 50% less VRAM
- **A3**: [View](A3/test-qwen-vl-20260910.md)
- **Recommendation**: 🟢 **Strong merge candidate**
- **Action**: Review A3; decide: merge to main or recycle for tuning

### 2. test-llava-multimodal-20260910
- **Hypothesis**: LLaVA improves caption quality for complex images
- **Result**: ⚠️ **Partially Supported** – Similar speed, better semantic understanding (subjective)
- **A3**: [View](A3/test-llava-multimodal-20260910.md)
- **Recommendation**: 🟡 **Promising but needs validation**
- **Action**: Review A3; suggest parameter sweep or user study

### 3. test-stable-diffusion-xl-20260910
- **Hypothesis**: SDXL generates higher-quality images than current model
- **Result**: ❌ **Falsified** – Slower (3x), higher VRAM, quality indistinguishable
- **A3**: [View](A3/test-stable-diffusion-xl-20260910.md)
- **Recommendation**: 🔴 **Not viable**
- **Action**: Kill experiment; save analysis for future reference

### 4. fix-label-render-edge-case-20260910
- **Hypothesis**: Handle Unicode text in label-render without crashes
- **Result**: ✅ **Supported** – All test cases pass, no regressions
- **A3**: [View](A3/fix-label-render-edge-case-20260910.md)
- **Recommendation**: 🟢 **Safe to merge**
- **Action**: Merge to main after code review

### 5. feature-prompt-optimization-20260910
- **Hypothesis**: Prompt engineering reduces output variance by 25%
- **Result**: ✅ **Supported** – Variance reduced 28%; statistical significance p<0.05
- **A3**: [View](A3/feature-prompt-optimization-20260910.md)
- **Recommendation**: 🟢 **Strong merge candidate**
- **Action**: Review A3; merge to main

---

## ⏳ In Progress (Running)

### 1. test-controlnet-fine-tuning-20260910
- **Started**: 2026-09-10 22:15 UTC
- **Expected completion**: 2026-09-11 04:30 UTC (6.25 hours)
- **Status**: Executing (2.5 hours elapsed, 50% complete)
- **Concourse link**: [Build #142](https://concourse.example.com/builds/142)
- **Action**: Monitor; will be included in tomorrow's digest if not complete by 08:00

---

## ❌ Failed Experiments

### 1. test-mistral-quantization-20260910
- **Started**: 2026-09-10 20:00 UTC
- **Failed**: 2026-09-10 20:45 UTC (45 min)
- **Error**: `RuntimeError: CUDA out of memory (requires 18GB, available 16GB)`
- **A3**: [Partial](A3/test-mistral-quantization-20260910.md) (shows problem, not results)
- **Recommendation**: 🟡 **Recycle with quantization adjustment**
- **Action**: Try 8-bit quantization or lower batch size; resubmit

---

## 🟡 Blocked Experiments

None currently.

---

## Your Decisions Needed (5 min review)

| Experiment | Status | Action | Your Call |
|---|---|---|---|
| test-qwen-vl-20260910 | ✅ Complete | Merge to main? | ☐ Yes ☐ No ☐ Recycle |
| test-llava-multimodal-20260910 | ✅ Complete | Further testing or kill? | ☐ Recycle ☐ Kill |
| test-stable-diffusion-xl-20260910 | ✅ Complete | Kill and archive? | ☐ Kill ☐ Keep experimenting |
| fix-label-render-edge-case-20260910 | ✅ Complete | Merge to main? | ☐ Yes ☐ No |
| feature-prompt-optimization-20260910 | ✅ Complete | Merge to main? | ☐ Yes ☐ No |
| test-mistral-quantization-20260910 | ❌ Failed | Retry with 8-bit? | ☐ Yes ☐ No |
| test-controlnet-fine-tuning-20260910 | ⏳ In Progress | — | (Check tomorrow) |

---

## Overnight Stats

- **Total Started**: 7
- **Completed**: 5 (71%)
- **Success Rate**: 4/5 (80%)
- **Avg Completion Time**: 6.2 hours
- **Total GPU Hours Used**: 31.4 hours
- **Cost (estimated)**: ~$4.71 @ $0.15/GPU-hour

---

**Generated by**: genops-morning-digest | **Next digest**: 2026-09-12 08:00 UTC
```

### 3. Post to Communication Channel

- **Slack/Discord** – Formatted digest with decision checkboxes
- **Email** – Same digest, for async review
- **Wiki repo** – Archive digest as `digests/<date>.md`

### 4. Parse Your Decisions

When you reply with checkboxes checked:

```
test-qwen-vl-20260910: ☑️ Yes
test-llava-multimodal-20260910: ☑️ Recycle
test-stable-diffusion-xl-20260910: ☑️ Kill
fix-label-render-edge-case-20260910: ☑️ Yes
feature-prompt-optimization-20260910: ☑️ Yes
test-mistral-quantization-20260910: ☑️ Yes
```

I execute:
- **Yes** → `git merge <branch> into main` + update Kanban to "Promoted"
- **Kill** → `git branch -d <branch>` + update Kanban to "Archived" + save A3
- **Recycle** → `git cherry-pick <changes>` onto new branch with adjusted params; resubmit

## Output

- **Digest posted** – Slack/email with decision checkboxes
- **Decisions logged** – Wiki repo `/digests/<date>.md`
- **Actions queued** – Ready to execute your merges/kills/recycled runs

## Anti-Rationalizations

- **"Why so much detail? Just tell me the winners."** → Null results and failures are as informative as successes. Buried failures → repeated experiments.
- **"Can I defer decisions?"** → Yes. "Hold" status keeps experiment in Kanban until you decide. But it consumes portfolio attention.
- **"Should I be surprised by failures?"** → No. ~20% failure rate is normal (CUDA OOM, race conditions, etc.). We retry, adjust, learn.

## Cron Configuration

```bash
# Run every morning at 08:00 UTC
0 8 * * * hermes cron run genops-morning-digest
```

Or on-demand:

```bash
hermes skills load genops-morning-digest
# Then trigger manually
```

## Notes

- Digest reflects state at 08:00 UTC; experiments that finish between 08:00-09:00 appear in next day's digest
- Blocking decision: nothing in Concourse proceeds until you decide (prevents accidental promotion)
- Digest is deterministic (same git state = same digest output; idempotent)
- A3 links are git-tracked and durable (won't disappear)
