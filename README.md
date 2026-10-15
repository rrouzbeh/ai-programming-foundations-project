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

## Reproducibility environment

Validated with Python 3.12 on macOS. `requirements.txt` is the full working
environment captured with `python -m pip freeze`; `requirements-minimal.txt`
retains the smaller set of direct dependencies for this project.

## Bias and Responsible Use

CISA KEV inclusion depends on documented evidence and publication decisions, so
catalog counts do not represent the frequency of all vulnerabilities or attacks.
Vendor deployment scale, detection resources, disclosure incentives, and reporting
delays can overrepresent widely monitored products and underrepresent others.
Cleaning can introduce further bias by silently dropping unrecognized CWE values
or treating an unknown ransomware association as evidence of no ransomware use;
this workflow retains the raw `cwes` and `knownRansomwareCampaignUse` fields and
reports the 10.22% missing-normalized-CWE rate for this snapshot.
Selecting only the first normalized CWE also loses information about
vulnerabilities with multiple weakness categories, so I would audit missing and
multiple-CWE records before comparing vulnerability types.
Operational prioritization should combine KEV membership with local asset
exposure, business criticality, compensating controls, and threat intelligence
instead of ranking risk by vendor frequency alone.

## Future Integration

### How would this workflow change in a full ML pipeline?

I would move `prepare_kev` and `summarize_kev` into a reusable module called by a
versioned ingestion and training pipeline, with schema validation and explicit
quality checks for missing fields, duplicate CVEs, and invalid dates.
I would define a prediction time and obtain a separate, meaningful target, such
as a locally measured remediation outcome, because this KEV-only snapshot cannot
establish exploit likelihood across all vulnerabilities.
I would split records chronologically into training, validation, and test sets
before learning imputations, encodings, or scaling parameters, and fit those
transformations only on training data.
Features such as `remediationDays` and `ransomwareKnown` would be used only if their
values were actually available at prediction time; CISA's deadline interval is a
policy measure, not an observed time to remediation.
The pipeline would save snapshot identifiers, preprocessing artifacts, model
versions, and evaluation results so each run could be reproduced and monitored.

### How would I prepare the data for neural network training?

I would use training-fitted categorical vocabularies and learned embeddings for
fields such as `vendorProject` and `primaryCWE`, with explicit missing and unseen
category handling, and consider a multi-label representation of the raw CWE list.
For `shortDescription`, I would evaluate a pretrained text encoder while checking
that its inputs contain no information added after the prediction cutoff.
I would scale numeric inputs such as `remediationDays` using training-set
statistics, retain missingness indicators, and encode date-derived seasonality
cyclically only where it is relevant to the prediction task.
I would build reproducible tensor batches and use validation data for model and
hyperparameter selection, reserving the chronological test set for final
evaluation.
With only this small catalog snapshot, I would compare the network against a
simple tabular baseline and check performance across vendors and missing-CWE
groups before deciding whether the added complexity is justified.

### How could agentic automation support this workflow?

An agent could retrieve a scheduled CISA snapshot, validate its schema, and compare
records against the previous version by `cveID` to identify additions and changes.
It could run `prepare_kev`, the existing quality assertions, and `summarize_kev`,
then regenerate the figures and draft a briefing from the returned summary
dictionary with links to the source records.
I would log snapshot hashes, changed CVEs, validation results, and generated
artifacts, and make reruns idempotent so the same update is not processed twice.
Schema changes or failed checks would stop publication and request human review;
external descriptions would be treated as data rather than agent instructions.
The agent would support analysis and propose priorities, while a person would
approve operational patching decisions and any external communications.
