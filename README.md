# Synthetic Healthcare Analytics

**Admission patterns, recorded billing, length of stay, and predictive evaluation**

**Author:** Tomisin Obijole

**Tools:** Python, Pandas, NumPy, Matplotlib, Seaborn, SciPy,
scikit-learn, Power Query, Power BI, and DAX

## Overview

This project explores synthetic healthcare admission records to examine
demographics, recorded conditions, admission patterns, length of stay,
billing, insurance, medications, and test-result categories.

It combines documented data preparation and exploratory analysis with
Power BI reporting and baseline predictive experiments.

The project also identifies questions that the source cannot reliably
answer, including clinical recovery, unique-patient readmission risk,
and department-level demand.

## Current Status

The revised analytical workflow is prepared. Final numerical findings,
model results, dashboard screenshots, and the PDF report require
execution and reconciliation against the original source CSV.

Results from the earlier analysis are not automatically treated as
results of the revised workflow.

## Analytical Questions

- How are admission records distributed across age, gender, and conditions?
- How do admission volumes vary over chronological months?
- How do stay durations differ across recorded groups?
- How do nonnegative recorded billing amounts vary by condition and
  admission type?
- Which insurer and provider labels appear most frequently?
- How are test-result categories and medication labels distributed?
- Which groups account for the most recorded stay-days?
- Do simple predictive models improve on baseline methods?
- Which requested analyses are unsupported by the available fields?

## Dataset

Source input page:
[Healthcare dataset](https://www.kaggle.com/code/manarmohamed24/analysis-healthcare-dataset/input).

The original file used in the earlier analysis contained
55,500 records and 15 columns.

Reported admission coverage was 8 May 2019–7 May 2024.
Confirm the exact coverage and source version during execution.

**Grain:** one synthetic admission record, not one verified unique patient.

Record the exact underlying dataset version, download date, and licence
before redistributing raw or processed data.

## Analytical Populations

| Population | Definition |
|---|---|
| Raw records | Original source file |
| Operational records | Deduplicated records passing age and date checks, regardless of billing sign |
| Financial records | Operational records with nonmissing, nonnegative Billing Amount |
| Quarantined records | Records failing documented age or date validation |

Negative billing does not automatically invalidate an admission record.
Such records remain operationally available when otherwise valid,
while clearly labelled financial measures exclude their amounts.

## Data Preparation

The notebook documents:

- Source schema, missingness, and file hash.
- Removal of exact raw duplicate copies under an explicit assumption.
- Text standardisation and additional exact-duplicate checks.
- Uppercase blood-type labels.
- Admission and discharge date validation.
- Age validation and correctly labelled inclusive age groups.
- Separate operational and financial populations.
- Record-level identifiers that are not presented as patient identifiers.
- Consistent exports for Power BI.

Age groups are 0–18, 19–35, 36–50, 51–65, and 66+.

## Exploratory Analysis

The analysis includes:

- Age and gender distributions.
- Condition counts and within-age condition percentages.
- Blood-type mix within conditions.
- Exploratory blood-type/condition association.
- Chronological admissions and observed-day rates.
- Stay duration by condition and admission type.
- Billing distributions with eligibility denominators.
- Insurer record counts.
- Provider-label sparsity and descriptive caseload.
- Test-result shares and recorded medication frequencies.
- Stay-day and concurrent-recorded-stay proxies.

Provider comparisons do not establish clinical quality.
Medication frequencies are not prescribing recommendations.

## Predictive Experiments

### Admission volume

Complete-month chronological backtesting compares:

- A trailing-12-month mean daily-rate baseline.
- A linear daily-rate trend.

The last six complete months are evaluated using expanding historical
training windows.

### Billing amount and length of stay

Fixed Ridge regression pipelines are compared with training-mean
baselines.

Evaluation uses chronological training, validation, and test partitions.

### Observed high billing

A training-only 75th-percentile billing threshold defines the
classification target.

Logistic regression is compared with a training-prevalence baseline.

Final conclusions depend on the executed results. Failure to improve
on a baseline is reported rather than replaced with unsupported
predictive claims.

See [model evaluation](docs/model_evaluation.md).

## Power BI Report Design

| Page | Purpose |
|---|---|
| Admissions Overview | Record counts, stay measures, and chronological admission activity |
| Demographics and Conditions | Age, gender, condition mix, and blood types |
| Billing and Insurance | Eligible billing records, amounts, exclusions, and insurers |
| Stay and Provider Labels | Stay duration, stay-days, and descriptive provider volumes |
| Predictive Evaluation and Limits | Baseline comparisons and unsupported questions |

The intended model uses FactAdmissions with an active one-to-many
date relationship.

Experimental model outputs are separate evaluation tables, not
individual clinical-risk dashboards.

## Key Interpretation Rules

- Admission records are not verified unique patients.
- Repeated names do not establish readmissions.
- A Normal test result does not establish recovery.
- Recorded billing is not profit, payments received, or verified cost.
- Billing currency is not assumed.
- Stay-days are not bed utilisation or staffing requirements.
- Room Number is an identifier, not an ordinal quality measure.
- Historical projections are anchored to the dataset cutoff,
  not the current date.

## Project Files

- [Analysis notebook](notebooks/Healthcare_Analysis.ipynb)
- [Power BI report](report/Healthcare_Admissions_Billing.pbix)
- [Insights report](docs/healthcare_insights_report.pdf)
- [DAX documentation](docs/dax_measures.md)
- [Question coverage](docs/question_coverage.csv)
- [Model evaluation](docs/model_evaluation.md)
- [Data instructions](data/README.md)
- [Demonstration guide](demo/README.md)

Generated findings and final report assets should be added only after
execution and validation.

## Run the Notebook

Create and activate a virtual environment:

```bash
python -m venv .venv
```

Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

macOS/Linux:

```bash
source .venv/bin/activate
```

Install dependencies and launch JupyterLab:

```bash
python -m pip install -r requirements.txt
python -m jupyterlab
```

Place `healthcare_dataset.csv` in `data/raw/`.
Open `notebooks/Healthcare_Analysis.ipynb` and run all cells in order.

## Refresh Power BI

1. Execute the notebook.
2. Import `data/processed/healthcare_records_v2.csv` as FactAdmissions.
3. Update Power Query source paths.
4. Validate data types and relationships.
5. Reconcile operational and financial measures separately.
6. Clear stored filters and restore full observed admission coverage.
7. Save `report/Healthcare_Admissions_Billing.pbix`.

GitHub provides the PBIX for download rather than rendering it as a
live Power BI report.

## Limitations

The dataset is synthetic and does not establish actual clinical,
financial, or operational relationships.

It lacks a verified patient identifier, clinical recovery outcome,
department field, bed capacity, staffing requirements, and medication
dose or consumption data.

Sparse provider labels, potential residual synthetic duplication,
partial boundary months, and uncertain billing semantics limit
interpretation.

Predictive experiments are educational and are not validated for
clinical deployment or real hospital planning.

## Author
![My Image](screenshots/personal/Tomisin.jpg)

**Tomisin Obijole**

- [GitHub](https://github.com/Tomisin510)
- [LinkedIn](https://www.linkedin.com/in/tomisin-obijole/)