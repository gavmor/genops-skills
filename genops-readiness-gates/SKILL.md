---
name: genops-readiness-gates
description: "Assess TRL/IRL/SRL maturity. Promotion gates, regression."
---

# Readiness Levels: TRL, IRL, SRL Assessment

Formally evaluate a workflow's maturity before promoting from experiment branch to mainline. Ensures only proven, low-risk specimens integrate.

## Three Readiness Dimensions

### Technology Readiness Level (TRL): Specimen Maturity

**Question:** Is the workflow mature and proven in isolation?

- **TRL 1-3: Exploratory** – Proof-of-concept, theory, isolated lab. Example: "Concept *might* work"
- **TRL 4-6: Development** – Component validation in simulated conditions. Example: "Works in 500-prompt validation set"
- **TRL 7-9: Deployment** – Proven through extended operations. Example: "1 week production, 100k+ renders, zero failures"

### Integration Readiness Level (IRL): Mainline Compatibility

**Question:** Does it integrate cleanly with mainline without breaking other workflows?

- **IRL 0-2: Isolated** – Standalone, no mainline interaction
- **IRL 3-5: Compatible** – Shares resources, no modifications to mainline
- **IRL 6-9: Integrated** – Replaces/extends mainline components

### System Readiness Level (SRL): Holistic Maturity

**SRL = min(TRL, IRL)**

Both dimensions must be strong. TRL=8 + IRL=3 → SRL=3 (not ready).

## Readiness Assessment Checklist

### TRL 7+ Validation (Required for Promotion)

- [ ] **Extended Operations** – ≥7 days production/production-like, ≥1000 renders, no critical failures
- [ ] **Edge Cases** – Tested adversarial/unusual inputs, graceful failures, max concurrency
- [ ] **Performance** – Latency measured under load, memory profiled (no leaks), GPU util monitored
- [ ] **Reproducibility** – Same input+seed = identical output (100+ times), consistent across hardware
- [ ] **Dependencies Pinned** – Model checksums versioned, software locked (container digest, pip freeze), external services have timeouts

### IRL 6+ Validation (Required for Promotion)

- [ ] **Regression Testing** – Full mainline test suite passed with new workflow integrated, ≥95% workflows acceptable, no perf degradation
- [ ] **Data Format** – Accepts standard input, output matches existing schema, no downstream consumer changes needed
- [ ] **Resource Sharing** – All shared resources identified, no conflicts under concurrency, load-tested (5 workflows parallel)
- [ ] **Error Handling** – Failures don't cascade, clear error messages, monitoring/alerting configured
- [ ] **Rollback** – <5 min rollback time, documented procedure, tested twice with zero data loss

## YAML Readiness Assessment Template

Store at `readiness-gates/<experiment-id>.yml` in wiki:

```yaml
---
experiment_id: feature/krea2-4step-distill-test
assessment_date: 2026-09-20
assessment_by: [Gavin Morgan, Alice (independent)]

trl_score: 8
trl_rationale: |
  7-day production trial: 100,021 renders, zero critical failures.
  Latency stable: 148.5s ± 9.8s (no degradation over time).
  Memory: 6.2GB peak, no leaks. Edge cases handled gracefully.
  Reproducibility: 100/100 tests passed (same seed = identical output).
  Hardware: tested RTX 3090, A100 (consistent).
  Dependencies: all pinned (model SHA256, PyTorch 2.0.1, CUDA 11.8).

irl_score: 7
irl_rationale: |
  Regression: 500 prompts through 8 production workflows, all passed.
  Performance: avg latency change -0.2% (within 5% tolerance).
  Resources: no conflicts, GPU memory unchanged.
  Error handling: OOM tested, graceful failure confirmed.
  Rollback: tested 2x, 3.2-min rollback time, zero data loss.
  Monitoring: Prometheus, Grafana, ELK logs configured.
  Phased rollout: canary 5% → 25% → 100% over 3 weeks.

srl_score: 7  # min(8, 7)
promotion_decision: GO
promotion_reasoning: |
  SRL=7 exceeds org threshold (SRL 6).
  TRL=8: technology mature (1 week production ops).
  IRL=7: integration ready (zero regression).
  Risk: LOW. Recommend mainline promotion with canary rollout.

approved_by: Independent Review Committee (Dave, Eve, Frank)
approval_date: 2026-09-20
```

## For ML/AI Workflows: MLTRL

ML models fail silently (plausible but wrong), not loudly (crash).

Add MLTRL checks:

- [ ] **Data Distribution Shift** – Tested on OOD data, graceful degradation, production monitoring for drift
- [ ] **Adversarial Robustness** – Jailbreak/adversarial prompts tested, no catastrophic failures, mitigations documented
- [ ] **Model Decay** – If frequently updated, update procedure tested, rollback confirmed, version control for checkpoints
- [ ] **Fairness & Bias** – Tested across diverse inputs, no systematic bias, limitations documented

## Workflow: Running Readiness Assessment

1. **Gather evidence:** Test results, logs, documentation for TRL/IRL checklists
2. **Self-assess:** Lead researcher completes checklist honestly
3. **Independent review:** Colleague unfamiliar with experiment reviews
4. **Committee gate:** Present to Independent Review Committee
5. **Decision:** Go/Hold/Recycle/Kill (recorded in YAML)
6. **If Go:** Delete `EXPERIMENT.yml` from branch, merge to `main`
7. **If Hold/Recycle/Kill:** Document, update Kanban

## Promotion Workflow Merge Checklist

Before merging experiment branch to `main`:

- [ ] Readiness assessment complete (TRL/IRL/SRL ≥ threshold)
- [ ] A3 finalized, filed in wiki
- [ ] Workflow file (`workflows/*.api.json`) final, tested
- [ ] SOP complete (`docs/SOP-*.md`)
- [ ] Supporting scripts/models archived (with checksums)
- [ ] Rollback plan tested
- [ ] **Delete `EXPERIMENT.yml` before merge** (signals promotion)
- [ ] PR reviewed by ≥1 independent reviewer
- [ ] Merge commit: `feat(workflows): promote [name] from [branch-id]`
- [ ] Post-merge: Update Kanban, start monitoring dashboards

## Common Rationalizations (and Why They Don't Work)

- **"TRL 6 is good enough for promotion."** → No. TRL 7+ is the gate. TRL 6 is "works in controlled conditions." TRL 7 is "proven in realistic operations." Those aren't negotiable.
- **"Regression testing is overhead; just ship it."** → Regression testing catches silent failures that user-facing tests miss. A PR with zero regressions is mergeable; anything else gets reverted.
- **"We can skip rollback testing; our deployments never fail."** → Famous last words. Test the rollback twice, document the procedure, or you will discover it doesn't work at 3am during an incident.
- **"One hardware config is enough; we don't need to test on multiple GPUs."** → Behavior can be hardware-specific (memory pressure, concurrency, clock speeds). Test on ≥2 configs or you will ship a silent failure.
- **"SRL 5 is fine; SRL 6+ seems excessive."** → SRL = min(TRL, IRL). If TRL=8 but IRL=3, SRL=3 (not ready). Both dimensions matter. Weak integration kills deployments.
