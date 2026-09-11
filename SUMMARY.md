# GenOps Skills Ecosystem — Complete & Ready

## Status ✅

**Local Repository:** `~/genops-skills`  
**Remote Configured:** `git@github.com:gavmor/genops-skills.git`  
**Commits:** 3 commits with all skills, documentation, anti-rationalizations, progressive disclosure

---

## What's Included

### **12 Skills Total**

**Governance & Tracking (7 skills)**:
1. `genops-framework` – Overview + principles
2. `genops-router` – Stage dispatcher (decision tree)
3. `genops-kanban` – Flow management + WIP
4. `genops-definition-of-done` – Completion gate
5. `genops-a3-report` – Visual abstract builder
6. `genops-readiness-gates` – TRL/IRL assessment
7. `genops-kill-matrix` – Portfolio rationalization

**Autonomous Runner (5 skills)**:
8. `genops-experiment-runner` – Submit link → spin pipeline
9. `genops-results-parser` – Extract ComfyUI metrics
10. `genops-a3-auto-renderer` – Auto-render A3 from results
11. `genops-morning-digest` – Daily digest with decisions
12. `genops-orchestrator` – Chain all into autonomous loop

---

## Key Features

### **Anti-Rationalization Tables**
- Each skill includes rebuttals to common excuses (per Addy Osmani)
- Bullet list format (WhatsApp-friendly)
- Specific, actionable (not generic essays)

### **Progressive Disclosure**
- `genops-router` guides which skill to load per stage
- No skill overload; context stays focused
- Autonomous runner can chain automatically via cron

### **Scope Discipline**
- Explicit guardrails in `genops-framework`
- Touch only what you're asked to touch
- No refactoring adjacent systems

### **High-Throughput Autonomous Execution**
- Submit link → overnight run → morning digest → 5-min review → execute decisions
- Parallel experiments (via Concourse instance pipelines)
- GPU lock ensures fair queuing

---

## Workflow Overview

```
You: Send link
  ↓ [genops-experiment-runner]
Scaffold branch + Concourse pipeline
  ↓ [Concourse overnight: 4-24 hours]
Download model → submit to ComfyUI → collect results
  ↓ [genops-results-parser]
Extract metrics (latency, VRAM, queue time)
  ↓ [genops-a3-auto-renderer]
Auto-render A3 (PDCA format)
  ↓ [genops-morning-digest @ 08:00]
Post summary with decision checkboxes
  ↓ [You: 5-min review]
Reply: merge / kill / recycle
  ↓ [genops-orchestrator]
Execute decisions (merge to main or retry)
  ↓ [Loop repeats next night]
```

---

## Files Included

```
~/genops-skills/
├── README.md                          # Full documentation
├── LICENSE                            # MIT
├── MANIFEST.yaml                      # Skill inventory + load order
├── PUSH_INSTRUCTIONS.md               # How to push to GitHub
│
├── genops-framework/SKILL.md          # Conceptual reference
├── genops-router/SKILL.md             # Dispatcher
├── genops-kanban/SKILL.md             # Flow management
├── genops-definition-of-done/SKILL.md # Completion gate
├── genops-a3-report/SKILL.md          # Visual abstract builder
├── genops-readiness-gates/SKILL.md    # TRL/IRL assessment
├── genops-kill-matrix/SKILL.md        # Portfolio rationalization
│
├── genops-experiment-runner/SKILL.md  # Runner: link → pipeline
├── genops-results-parser/SKILL.md     # Runner: parse metrics
├── genops-a3-auto-renderer/SKILL.md   # Runner: render A3
├── genops-morning-digest/SKILL.md     # Runner: daily digest
├── genops-orchestrator/SKILL.md       # Runner: chain + execute
│
└── .git/                              # 3 commits, ready to push
```

---

## To Push to GitHub

### Step 1: Create Empty Repository on GitHub

Visit: https://github.com/new

- **Owner**: gavmor
- **Name**: genops-skills
- **Visibility**: Public
- **Initialize with**: *(leave blank)*

### Step 2: Push

```bash
cd ~/genops-skills
git push -u origin main
```

### Step 3: Verify

Visit: https://github.com/gavmor/genops-skills

You should see:
- 12 skill directories
- README.md with full documentation
- LICENSE (MIT)
- MANIFEST.yaml
- 3 commits in history

---

## Installation After Push

```bash
# Install all skills
hermes skills import \
  genops-framework \
  genops-router \
  genops-kanban \
  genops-definition-of-done \
  genops-a3-report \
  genops-readiness-gates \
  genops-kill-matrix \
  genops-experiment-runner \
  genops-results-parser \
  genops-a3-auto-renderer \
  genops-morning-digest \
  genops-orchestrator
```

Or clone from GitHub:

```bash
git clone https://github.com/gavmor/genops-skills.git ~/.hermes/skills/genops
```

---

## Cron Configuration (Optional)

Set up automated cadence:

```yaml
jobs:
  - name: genops-morning-digest
    schedule: "0 8 * * *"  # 08:00 UTC daily
    prompt: "Load genops-morning-digest. Post overnight results with decision checkboxes."
    attach_to_session: true
```

---

## Key Principles Encoded

1. **Process over prose** – Workflows with checkpoints, not essays
2. **Anti-rationalization tables** – Pre-written rebuttals
3. **Verification non-negotiable** – Every workflow ends in evidence
4. **Progressive disclosure** – Load only relevant skills
5. **Scope discipline** – Touch only what you're asked
6. **No career penalty** – Kill decisions are operational milestones
7. **Idempotent** – Same git state = same results
8. **Reversible** – All decisions are git operations (undo via revert)

---

## Design Inspiration

- **Kanban**: Toyota Production System, Lean methodology
- **Agile-Stage-Gate**: Robert Cooper
- **A3 Problem-Solving**: Toyota
- **TRL/IRL/SRL**: NASA, U.S. DoD, aerospace
- **Sunk Cost Kill Matrix**: Behavioral economics (Thaler, Arkes, Blumer)
- **Agent Skills**: Addy Osmani (addyosmani.com/blog/agent-skills/)

---

## Next Steps

1. **Push to GitHub** (see "To Push to GitHub" section above)
2. **Install skills** into your Hermes profile
3. **Send first experiment link** – "Hey, check out this model..."
4. **Overnight run** – I scaffold + execute
5. **Morning digest** – Review results + decide (5 min)
6. **Decisions execute** – Merge, kill, or recycle

---

## Questions?

All skills include:
- Workflow steps (not essays)
- Input/output specs
- Anti-rationalizations (rebuttals to excuses)
- Notes and guardrails

Load any skill to understand its role:

```bash
hermes skills load genops-experiment-runner
```

---

**Ready to revolutionize your R&D operation. 🚀**
