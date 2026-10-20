# Data Sources and Analytical Populations

## Source

Dataset input page:
https://www.kaggle.com/code/manarmohamed24/analysis-healthcare-dataset/input

Obtain the healthcare CSV linked to the source analysis.

The earlier project file contained 55,500 records and 15 columns.
Confirm the loaded schema, date coverage, and source version rather
than assuming every download contains the same data.

Record the original dataset licence before redistribution.

## Local Layout

```text
data/
├── README.md
├── raw/
│   └── healthcare_dataset.csv
└── processed/
    └── generated analysis files
```

Run `notebooks/Healthcare_Analysis.ipynb` to generate the processed files.

## Original Fields

Name, Age, Gender, Blood Type, Medical Condition, Date of Admission,
Doctor, Hospital, Insurance Provider, Billing Amount, Room Number,
Admission Type, Discharge Date, Medication, and Test Results.

## Record Grain

One row represents a synthetic admission record.

Name is not a verified patient identifier. Generated RecordID values
identify records only and must not be used as longitudinal patient IDs.

## Cleaning Policy

1. Preserve the original source file.
2. Remove exact raw duplicate copies under a documented assumption.
3. Standardise text and check for additional exact duplicate copies
   across all original fields.
4. Do not deduplicate on Name alone.
5. Preserve uppercase blood-type labels.
6. Parse dates and validate their order.
7. Validate age values.
8. Separate operational eligibility from financial eligibility.
9. Document quarantined records and financial exclusions.

Negative billing is not automatically classified as error or refund.
Its business meaning is unavailable from the source alone.

## Analytical Populations

### Operational records

Deduplicated records passing the documented date and age checks.

Negative-billing records remain included when otherwise valid.

Used for demographics, admission counts, stay duration, test-result
distributions, and descriptive provider volumes.

### Financial records

Operational records with a nonmissing Billing Amount greater than
or equal to zero.

Used for nonnegative recorded billing summaries and the current
predictive billing experiments.

### Quarantined records

Records failing date or age validation.

Their counts and reasons are recorded separately.

## Age Groups

| Label | Inclusive ages |
|---|---|
| 0–18 | 0 through 18 |
| 19–35 | 19 through 35 |
| 36–50 | 36 through 50 |
| 51–65 | 51 through 65 |
| 66+ | 66 through the accepted maximum age |

These are analytical groups, not clinical definitions of senior or
geriatric status.

## Principal Generated Files

| File | Purpose |
|---|---|
| healthcare_records_v2.csv | Operational fact records with financial eligibility flags |
| billing_nonnegative_v2.csv | Financial-population reconciliation |
| quarantine_records.csv | Records failing age/date checks |
| cleaning_audit.csv | Cleaning and population counts |
| monthly_admissions_v2.csv | Monthly counts, observed days, and coverage flags |
| question_coverage.csv | Evidence and limitations for 28 analytical questions |
| condition_billing.csv | Condition-level financial summaries |
| provider_hospitals.csv | Hospital-label volumes and descriptive result shares |
| provider_doctors.csv | Doctor-label volumes and descriptive result shares |
| normal_results_by_condition.csv | Normal-result counts and shares |
| resource_proxies.csv | Record and stay-day summaries |
| medication_counts.csv | Recorded medication frequencies |
| recorded_stay_census.csv | Illustrative concurrent-stay proxy |
| admission_forecast_backtest.csv | Actual and predicted monthly counts |
| forecast_metrics.csv | Forecast MAE and RMSE |
| historical_origin_projection.csv | Educational projections from the historical cutoff |
| regression_metrics.csv | Billing/LOS baseline and candidate-model results |
| classification_metrics.csv | High-billing classification evaluation |
| heldout_regression_predictions.csv | Held-out regression outputs |
| heldout_classification_predictions.csv | Held-out experimental probabilities |

Public fact exports omit Name. This is an analytical presentation choice,
not a claim that generated RecordID establishes patient identity.

## Additional Generated Evidence

The notebook also writes:

- `docs/run_manifest.json`
- `docs/executed_evidence_summary.md`
- Analysis chart images in `figures/`

The manifest records the source hash, population snapshot, split dates,
and training-only billing threshold.

## Question Coverage for Publication

The notebook initially creates:

`data/processed/question_coverage.csv`

Copy the final reviewed file to:

`docs/question_coverage.csv`

It documents which questions are descriptive, experimental, restricted,
or unavailable from this source.

## Power BI Import

Import `healthcare_records_v2.csv` as FactAdmissions.

Retain RecordID and room/provider labels as identifiers. Use Date types
for AdmissionDate and DischargeDate, numeric types for billing and stay
duration, and Boolean types for eligibility flags.

Do not add pre-aggregated monthly or condition totals to the fact table.

Predictive metrics and backtest outputs are separate evaluation tables.
They do not represent individual clinical-risk assessments.

## Reconciliation

Use the newly executed notebook, cleaning audit, and run manifest as
the reference for dashboard totals.

Do not force the revised population to equal the earlier 54,860 records:
the revised workflow retains negative-billing records operationally and
checks for additional duplicates after normalisation.

## Data Licence and Interpretation

Verify the original source licence before committing raw or processed data.
A licence applied to project code does not automatically cover source data.

Synthetic records cannot establish real-world disease prevalence,
recovery, treatment effectiveness, provider quality, or resource demand.