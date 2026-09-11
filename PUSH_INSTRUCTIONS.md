# Pushing GenOps Skills to GitHub

## Local Repository Status

✅ Repository initialized at: `/home/user/genops-skills`
✅ Commit created: `bc00d15` (all skills, docs, LICENSE, MANIFEST)
✅ Remote configured: `origin` → `git@github.com:gavmor/genops-skills.git`

## Next Steps

### 1. Create the Repository on GitHub

Visit: https://github.com/new

Fill in:
- **Owner**: gavmor
- **Repository name**: genops-skills
- **Description**: Generative Operations Protocol: modular skills for orchestrating high-throughput R&D via Kanban, Definition of Done, A3 reports, readiness gates, and kill matrix
- **Visibility**: Public
- **Initialize with**: Nothing (we have local commits already)

### 2. Push the Repository

```bash
cd ~/genops-skills
git push -u origin main
```

If SSH key authentication works:
```bash
git push -u origin main
```

If using HTTPS, follow GitHub's personal access token flow.

### 3. Verify

Visit: https://github.com/gavmor/genops-skills

You should see:
- 8 skill directories
- README.md (overview)
- LICENSE (MIT)
- MANIFEST.yaml (skill inventory)

## Contents

- **genops-framework** – Overview and principles
- **genops-router** – Skill dispatcher (use first to decide which skill applies)
- **genops-experiment-init** – Scaffold new experiments
- **genops-kanban** – Board management and flow metrics
- **genops-definition-of-done** – Completion verification
- **genops-a3-report** – Visual abstract generation
- **genops-readiness-gates** – TRL/IRL/SRL assessment
- **genops-kill-matrix** – Portfolio rationalization

## Once Pushed

### Make Skills Available Locally
```bash
# Install into your Hermes profile
hermes skills import \
  genops-framework \
  genops-router \
  genops-experiment-init \
  genops-kanban \
  genops-definition-of-done \
  genops-a3-report \
  genops-readiness-gates \
  genops-kill-matrix
```

Or clone the repo and symlink:
```bash
git clone https://github.com/gavmor/genops-skills.git ~/.hermes/skills/genops
```

### Usage

Start with the router to decide which skill to load:
```
Load: genops-router
→ Describes what stage your experiment is in
→ Routes to the correct sub-skill
→ Load that skill and follow the workflow
```

Example flow:
1. `genops-router` (decision tree)
2. `genops-experiment-init` (create branch + EXPERIMENT.yml)
3. `genops-kanban` (move to Active Testing)
4. `genops-a3-report` (build abstract)
5. `genops-definition-of-done` (verify completion)
6. `genops-readiness-gates` (assess TRL/IRL)
7. Promotion merge to main
8. Quarterly: `genops-kill-matrix` (score portfolio)

## Files Included

```
genops-skills/
├── README.md                         # Overview + quick start
├── LICENSE                           # MIT
├── MANIFEST.yaml                     # Skill inventory + load order
├── genops-framework/SKILL.md         # Conceptual reference
├── genops-router/SKILL.md            # Dispatcher
├── genops-experiment-init/SKILL.md   # Experiment scaffolding
├── genops-kanban/SKILL.md            # Flow management
├── genops-definition-of-done/SKILL.md # Completion gate
├── genops-a3-report/SKILL.md         # Visual abstracts
├── genops-readiness-gates/SKILL.md   # TRL/IRL assessment
├── genops-kill-matrix/SKILL.md       # Portfolio rationalization
└── .git/                             # Git history
```

## Questions or Issues?

See the README.md for full documentation and references.
