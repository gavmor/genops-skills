---
name: genops-experiment-runner
description: "Submit link, spin Concourse pipeline, kick off."
---

# Autonomous Experiment Runner

Use this skill to take a link (HuggingFace model, GitHub workflow, Reddit post, etc.), scaffold a Concourse pipeline, and submit the experiment for overnight execution.

## Input

You provide:
- **Link** – URL to model, workflow, or technique description
- **One-line hypothesis** – What are we testing? (optional; I infer from link)

## Workflow

### 1. Extract Metadata from Link

- **HuggingFace Model**: Extract model ID, download URL, description from model card
- **GitHub Repo**: Clone, find README/model files, extract architecture + usage
- **Reddit/Blog Post**: Parse link title, description, any claimed results
- **Output**: EXPERIMENT.yml stub with hypothesis, problem_statement, primary_artifact URL

### 2. Create Feature Branch

```bash
git checkout -b feature/test-<model-id>-<date>
```

Branch naming: `feature/test-<source>-<brief>-<YYYYMMDD>`
- Example: `feature/test-huggingface-qwen-vl-20260910`

### 3. Scaffold EXPERIMENT.yml

```yaml
---
experiment_id: "test-<model-id>-<date>"
lead_researcher: "autonomous-runner"
start_date: "<today>"
target_completion: "<tomorrow>"

hypothesis: "<extracted from link or user-provided>"
problem_statement: "Evaluate <source> against baseline"
strategic_alignment: "Tier-3: exploratory evaluation"

primary_artifacts:
  - "<model-id or workflow>"
  - "source_url: <link>"

analysis_scripts:
  - "analysis/parse_metrics.py"
  - "analysis/render_a3.py"

kanban_stage: "Active Testing"
kanban_stage_entered: "<timestamp>"

notes: "Autonomously submitted by genops-experiment-runner from <link>"
```

### 4. Generate Concourse Pipeline

Create `concourse/pipeline.yml` on the branch:

```yaml
resources:
  - name: model-or-workflow
    type: git
    source:
      uri: <extracted-from-link>
      branch: main
      private_key: ((github_private_key))

jobs:
  - name: run-experiment
    plan:
      - get: model-or-workflow
        trigger: true
      - put: gpu-lock
        params: { acquire: true }
      - task: execute-experiment
        config:
          platform: linux
          image_resource:
            type: docker-image
            source:
              repository: comfyui-local
          run:
            path: /bin/bash
            args:
              - -c
              - |
                cd /workspace
                # Download model from HuggingFace or clone repo
                python3 download_model.py <model-id>
                # Submit to ComfyUI via HTTP
                curl -X POST http://comfyui-local:8188/prompt \
                  -H "Content-Type: application/json" \
                  -d @workflow.api.json > results.json
                # Poll for completion
                python3 poll_results.py results.json
                # Extract metrics (latency, VRAM, queue time)
                python3 extract_metrics.py > metrics.json
        ensure:
          - put: gpu-lock
            params: { release: gpu-lock }
      - task: parse-and-upload
        config:
          platform: linux
          image_resource:
            type: docker-image
            source:
              repository: alpine
          run:
            path: /bin/sh
            args:
              - -c
              - |
                # Parse outputs
                python3 parse_outputs.py
                # Upload to R2
                rclone copy outputs/ esoteria-r2:esoteria-assets/experiments/<experiment-id>/
                # Write metrics to artifact
                cp metrics.json ../outputs/
      - put: wiki-repo
        params:
          repository: wiki-repo
          rebase: true
```

### 5. Commit and Push

```bash
git add EXPERIMENT.yml concourse/pipeline.yml
git commit -m "[genops-experiment-runner] test-<model-id>: <hypothesis>"
git push origin feature/test-<model-id>-<date>
```

Concourse detects branch, spins up instance pipeline via `set-branch-pipelines`.

### 6. Kick Off Pipeline

```bash
fly trigger-job -j blades68/<instance-pipeline>/run-experiment
```

Or set `trigger: true` on git resource (auto-runs on commit).

## Output

- **Pipeline started** – Concourse job running
- **Monitoring URL** – Link to Concourse build logs
- **Expected completion** – Timestamp estimate (based on typical ComfyUI run time)
- **EXPERIMENT.yml committed** – Branch ready for polling

## Anti-Rationalizations

- **"Should I wait for the pipeline to finish before returning?"** → No. Submit and return immediately. Polling happens asynchronously via `genops-morning-digest`.
- **"Should I pre-validate the link?"** → Yes. If link is 404 or clearly invalid, stop and ask user. Otherwise, proceed optimistically; pipeline failure is fine—we learn why it failed.
- **"What if the model doesn't fit in VRAM?"** → Pipeline fails, we see the error, we kill the experiment or adjust parameters. Failure is data.

## Notes

- Experiment starts in **Active Testing** stage (already executing)
- No human gate required to start; you own the risk of running untested code
- Pipeline failure is expected (test coverage, not production requirement)
- Use `genops-results-parser` and `genops-a3-auto-renderer` downstream
