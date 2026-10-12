# Reproducible CISA KEV Workflow

This project loads, cleans, summarizes, and visualizes the official CISA Known
Exploited Vulnerabilities catalog. It includes documented reusable functions,
quality assertions, three figures, an executed notebook, and an academic report.

Dataset: [CISA Known Exploited Vulnerabilities Catalog](https://www.cisa.gov/known-exploited-vulnerabilities-catalog)

## Run

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook data_workflow.ipynb
```

Run all notebook cells from top to bottom.

## Git history and repository submission

The ZIP includes `project_1_history.bundle`, preserving the local commits and
`visualization-development` branch. To restore a repository from that bundle:

```bash
git clone project_1_history.bundle ai-programming-foundations-project
cd ai-programming-foundations-project
git branch visualization-development origin/visualization-development
```

The public repository contains the project, multiple commits, and both branches:
https://github.com/rrouzbeh/ai-programming-foundations-project

Supply this URL in the course submission form when the repository link is requested.

## Reproducibility environment

Validated with Python 3.12 on macOS. `requirements.txt` is the full working
environment captured with `python -m pip freeze`; `requirements-minimal.txt`
retains the smaller set of direct dependencies for this project.
