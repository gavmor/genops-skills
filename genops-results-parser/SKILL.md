---
name: genops-results-parser
description: "Parse outputs: extract latency, VRAM, queue time."
---

# Results Parser: Metrics Extraction

Use this skill to automatically extract ComfyUI standard metrics from experiment results and write them into EXPERIMENT.yml and prepare data for A3 rendering.

## Input

- **Concourse artifact store** – `metrics.json` from pipeline execution
- **EXPERIMENT.yml** – Current experiment metadata
- **Outputs** – Images/results uploaded to R2

## Workflow

### 1. Parse Concourse Logs

Extract timing from ComfyUI queue logs:

```python
import json
import re

# Concourse task log format:
# [ComfyUI] Queue time: 12.34s
# [ComfyUI] Execution time: 45.67s
# [ComfyUI] Peak VRAM: 8192 MB

metrics = {
    "queue_time_sec": 12.34,
    "execution_time_sec": 45.67,
    "peak_vram_mb": 8192,
    "total_time_sec": 58.01,
    "timestamp": "2026-09-10T22:55:30Z",
    "status": "success"
}
```

### 2. Compare to Baseline

If baseline workflow exists on `main` branch:

```python
baseline = {
    "execution_time_sec": 120.0,
    "peak_vram_mb": 16384
}

delta = {
    "execution_time_vs_baseline": (45.67 - 120.0) / 120.0 * 100,  # -61.9%
    "vram_vs_baseline": (8192 - 16384) / 16384 * 100  # -50.0%
}
```

### 3. Write Results to EXPERIMENT.yml

Append metrics section:

```yaml
---
# ... existing fields ...

results:
  execution_time_sec: 45.67
  queue_time_sec: 12.34
  peak_vram_mb: 8192
  total_time_sec: 58.01
  status: "success"
  timestamp: "2026-09-10T22:55:30Z"
  
comparison_to_baseline:
  execution_time_delta_pct: -61.9
  vram_delta_pct: -50.0
  verdict: "Faster and more efficient"

outputs:
  - "s3://esoteria-assets/experiments/<experiment-id>/output_00.png"
  - "s3://esoteria-assets/experiments/<experiment-id>/output_01.png"
```

### 4. Synthesize Findings

Auto-generate a preliminary conclusion:

```python
if delta['execution_time_vs_baseline'] < -20:  # >20% faster
    finding = "Significantly faster than baseline"
elif delta['execution_time_vs_baseline'] > 20:  # >20% slower
    finding = "Slower than baseline; likely due to <reason>"
else:
    finding = "Similar performance to baseline"

if delta['vram_vs_baseline'] < -30:  # >30% less VRAM
    finding += "; substantially more efficient"
```

## Output

- **EXPERIMENT.yml** – Updated with results section
- **metrics.json** – Extracted metrics for A3 rendering
- **comparison.json** – Delta vs. baseline
- **Status** → Move to "Data Analysis" stage

## Anti-Rationalizations

- **"What if parsing fails?"** → Log the error, halt progression, alert user. Don't guess metrics.
- **"Should I smooth outliers?"** → No. Report raw values. Outliers are interesting; don't hide them.
- **"What if there's no baseline?"** → Skip comparison, report absolute metrics. Delta is optional.

## Notes

- Runs immediately after Concourse job completes
- Part of the automated overnight pipeline (no user intervention)
- Metrics are deterministic (ComfyUI log format is stable)
- Use output for `genops-a3-auto-renderer` input
