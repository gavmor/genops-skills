---
name: genops-definition-of-done
description: "Verify experiment complete: DoD, reproducibility, data."
---

# Definition of Done (DoD)

Before an experiment passes a gate review, it must satisfy strict Definition of Done. Prevents "90% done" syndrome.

## Universal DoD Checklist

### 1. Execution Completeness

- [ ] All planned iterations executed
- [ ] Technical replicates (N ≥3) completed
- [ ] Control groups and baselines run
- [ ] No undocumented protocol deviations
- [ ] Failures recorded and resolved

**Verify:** Workflow files complete, commit history shows all planned tests, deviations documented.

### 2. Data Integrity & Ingestion

- [ ] Raw data archived (wiki repo `experiments/<exp-id>/data/`)
- [ ] Instrument telemetry and logs recorded
- [ ] Metadata schemas applied consistently
- [ ] Data versioning clear (raw vs. processed, lineage)

**Verify:** Wiki directory exists with dated subdirs, metadata headers present, README explains provenance.

### 3. Statistical Analysis

- [ ] Raw data processing complete
- [ ] Confidence intervals, std dev, or quantile ranges computed
- [ ] p-values and statistical tests documented
- [ ] Anomalies and outliers explicitly annotated
- [ ] Mathematical justification for anomaly handling

**Verify:** Scripts run without error, outputs include uncertainty bands, A3 shows statistical summaries.

### 4. Reproducibility & Codification

- [ ] SOP formalized (Standard Operating Procedure)
- [ ] SOP detail sufficient for independent replication
- [ ] All dependencies documented (versions, hardware, reagents)
- [ ] Seed values, random state explicitly logged
- [ ] Reproduction script demonstrates replicability

**Verify:** `docs/SOP-<experiment-id>.md` exists and is self-contained, unfamiliar researcher can run workflows, deps pinned.

### 5. Definitive Synthesis

- [ ] Clear determination: hypothesis supported/partial/falsified
- [ ] Findings explicitly stated (not hedged)
- [ ] Implications for mainline documented
- [ ] Next steps identified (promote/recycle/archive)

**Verify:** A3 "Conclusion" section unambiguous, addresses original hypothesis, proposed next action clear.

### 6. Visual Documentation

- [ ] A3 report complete and formatted
- [ ] Visual hierarchy: critical finding is largest element
- [ ] Typography consistent (max 3 sizes, sans-serif)
- [ ] Color palette accessible (colorblind-safe)
- [ ] 20-30% white space preserved
- [ ] A3 filed at `A3/<experiment-id>.md` in wiki

**Verify:** Render to PDF, scan in 10 seconds, charts labeled with units/axes/legends.

## Domain-Specific Extensions

### Computational / ML

- [ ] Model checkpoints versioned (git SHAs, release tags)
- [ ] Hyperparameters logged (config files committed)
- [ ] Training curves show convergence
- [ ] Validation set performance confirmed
- [ ] Inference latency and compute profiled

### Rendering / Generative

- [ ] Output samples curated (best, median, worst)
- [ ] Seed reproducibility confirmed (same seed = identical output)
- [ ] Parameter sensitivity tested
- [ ] Batch consistency verified
- [ ] Failure cases documented

### Hardware / Physical

- [ ] Environmental conditions logged
- [ ] Instrument calibration current
- [ ] Maintenance logs checked
- [ ] Material batch numbers documented
- [ ] Consumable wear tracked

## Workflow: Running DoD Verification

1. **Self-assess:** Lead researcher completes checklist honestly
2. **Address gaps:** Complete work or document deviations
3. **Peer verify:** Colleague unfamiliar with experiment reviews independently
4. **A3 finalize:** Ensure wiki A3 is complete, well-formatted
5. **Gate review:** Present DoD checklist, A3, and data to steering committee

## Key Principle: No "90% Done"

If item says "done" but isn't, that's a failure. Better to return to "Recycle" for rework than promote flawed work.

## Common Rationalizations (and Why They Don't Work)

- **"This experiment is too simple to need a full DoD."** → DoD checklist has six items; if you can't check them in 10 minutes, they're not optional—they're urgent. Even "simple" experiments break on reproducibility and data integrity.
- **"Tests pass, that's enough."** → Passing tests are evidence, not proof. Did you check runtime latency? Did you verify seed reproducibility? Did a peer review the A3? Verification is non-negotiable.
- **"I'll write the SOP after we promote to mainline."** → There is no "after." SOP must exist before Peer Review gate, or reproducibility claim fails. Write it during Active Testing.
- **"The results speak for themselves."** → No. Findings without an A3 abstract are just claims. The A3 is proof the experiment is complete.
- **"We can skip statistical rigor for a preliminary test."** → Preliminary or not, anomalies and outliers must be annotated. Unannotated data is unusable data.
- **"One colleague reviewed it; that's enough."** → Peer review means independent verification by someone unfamiliar with the experiment. Hallway code review doesn't count.
