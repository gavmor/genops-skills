---
name: genops-a3-auto-renderer
description: "Auto-render A3 from results, hypothesis, metrics."
---

# A3 Auto-Renderer: Visual Abstract Generation

Use this skill to automatically render a one-page A3 "mini poster" from experiment results, combining extracted metrics, outputs, and hypothesis into PDCA-formatted markdown.

## Input

- **EXPERIMENT.yml** – Full experiment metadata, hypothesis, problem_statement, results section
- **metrics.json** – Extracted from `genops-results-parser`
- **outputs/** – Images/results from R2 (links or local copies)
- **comparison.json** – Delta vs. baseline (if available)

## Workflow

### 1. Structure PDCA Layout (Markdown + Embedded Images)

Generate `A3/<experiment-id>.md` with six sections in reading order (top-left → bottom-right):

```markdown
# A3: <Experiment ID> | <Date>

**Lead**: <lead_researcher> | **Hypothesis**: <hypothesis in <20 words>

---

## Problem Statement (Top-Left)

<problem_statement from EXPERIMENT.yml>

**Strategic Alignment**: <strategic_alignment>

---

## Current Condition (Middle-Left)

Baseline performance (from main branch):
- **Execution Time**: 120.0s
- **Peak VRAM**: 16384 MB
- **Throughput**: X images/minute

---

## Hypothesis & Proposed Change (Bottom-Left)

**Hypothesis**: <hypothesis>

**Method**: Test <model-id> from <source_url>

**Expected Outcome**: <extraction or inference from link>

---

## Experimental Design (Top-Right)

**Setup**:
- Model: <model-id>
- Input: <workflow description>
- Runs: 1 (single execution)
- Controls: Baseline workflow from main

**Procedure**:
1. Download model
2. Submit to ComfyUI
3. Collect outputs + metrics
4. Compare to baseline

---

## Results & Data Visualization (Middle-Right)

**Metrics Achieved**:
- **Execution Time**: 45.67s
- **Peak VRAM**: 8192 MB
- **Queue Time**: 12.34s
- **Total Time**: 58.01s

**vs. Baseline**:
- ⬇️ **Execution**: -61.9% (45.67s vs 120.0s)
- ⬇️ **VRAM**: -50.0% (8192 MB vs 16384 MB)
- ✅ **Status**: Success

**Sample Output**:
![Result Image 1](s3://esoteria-assets/experiments/<experiment-id>/output_00.png)

---

## Conclusion & Next Steps (Bottom-Right)

**Finding**: 
<Synthesized finding from results>

- ✅ Hypothesis: Supported | ⚠️ Partially Supported | ❌ Falsified
- **Recommendation**: 
  - [ ] Promote to main (proven faster + efficient)
  - [ ] Recycle with parameter tuning (promising but needs work)
  - [ ] Archive (not viable)

**Next Steps** (if not promoting):
1. <Action 1>
2. <Action 2>

---

**Experiment Metadata**:
- ID: <experiment_id>
- Branch: <git branch>
- Pipeline: <Concourse build link>
- Results: <R2 folder link>
```

### 2. Auto-Render Markdown to HTML/PNG

Optional: Convert to visual "poster" format using:
- Markdown → HTML via `pandoc`
- HTML → PNG via `wkhtmltopdf` or browser headless capture
- Upload PNG to wiki repo or R2

Alternatively: Keep as markdown (simpler, git-friendly, human-readable).

### 3. Commit to Wiki Repo

```bash
git clone <wiki-repo>
cp A3/<experiment-id>.md wiki-repo/A3/
git add A3/<experiment-id>.md
git commit -m "[genops-a3-auto-renderer] <experiment-id>: <hypothesis>"
git push origin main
```

### 4. Update Main Branch EXPERIMENT.yml

Add A3 link to experiment branch:

```yaml
a3_report: "A3/<experiment-id>.md"
a3_url: "https://wiki-repo/A3/<experiment-id>.md"
```

## Output

- **A3/<experiment-id>.md** – One-page visual abstract in wiki repo
- **Status** → Move to "Peer Review" stage
- **Ready for review** → User sees A3, decides go/kill/recycle

## Anti-Rationalizations

- **"Should I polish the A3?"** → No. Make it readable but don't spend hours on design. Signal is what matters.
- **"What if results are inconclusive?"** → That's fine. A3 shows inconclusive finding. You still decide next step.
- **"Should I hide negative results?"** → Absolutely not. Null results and failures are as valuable as successes. Show them.

## Visual Design Guidelines

(From original A3 research; enforce in rendered output)

- **Hierarchy**: 2-3 font sizes (title, section headers, body)
- **Color**: 2-4 color palette max (one per section)
- **White Space**: 20-30% (don't crowd)
- **Accessibility**: Color-blind safe palette (avoid red-green)
- **Readability**: Section headers in bold, metrics in monospace or bold

## Notes

- Markdown A3 is git-tracked (version history preserved)
- HTML/PNG rendering is optional (nice-to-have for visual-first reviews)
- A3 generation is fast (~1 min) and happens after results parsing
- A3 is the human-facing summary; you review this, not the raw logs
