# Model Evaluation

**Project:** Synthetic Healthcare Analytics  
**Author:** Tomisin Obijole

## Evaluation Status

The revised experiments are implemented in the analysis notebook.
Results must be populated from execution on the original source CSV.

No revised performance figures are asserted in this document.

The earlier notebook's model results belong to a different workflow
and are not evidence of the revised models' performance.

## Purpose

Evaluate whether simple models improve on transparent baselines for:

1. Monthly admission-record counts.
2. Recorded billing amount.
3. Length of stay.
4. Classification of observed high billing.

These are synthetic-data experiments, not validated clinical systems.

## Data Populations

Forecasting uses the operational population and complete admission months.

Billing regression, length-of-stay regression, and high-billing
classification currently use the financial population:
operational records with nonmissing, nonnegative billing.

The length-of-stay experiment therefore describes that restricted cohort,
not all operational records.

Exact duplicates are removed under the documented cleaning assumptions.
Residual near-duplicate synthetic records remain a potential limitation.

## Experiment 1: Admission-Volume Forecasting

### Target

Monthly admission-record count.

### Input Preparation

- Create chronological year-month counts.
- Calculate observed days and records per observed day.
- Exclude partially observed boundary months.
- Require at least 24 complete months for the demonstration.

### Baseline

Mean daily admission-record rate over the preceding 12 complete months,
multiplied by the target month's calendar-day count.

### Candidate

Linear regression of historical daily admission-record rate against
chronological month position.

Negative predicted rates are clipped to zero before conversion to counts.

### Evaluation

For each of the final six complete months:

1. Train using earlier complete months only.
2. Predict the next month.
3. Record the actual count and both predictions.

This is expanding-window, one-step-ahead evaluation.

### Metrics

- Mean absolute error: admission records.
- Root mean squared error: admission records.

Lower values indicate better predictive accuracy.

### Result Files

- `data/processed/admission_forecast_backtest.csv`
- `data/processed/forecast_metrics.csv`

### Projection Scope

The three-month projection begins after the last complete historical
month in the dataset. It is not a forecast from the current date.

It may overlap the partially observed final month.

The notebook does not provide calibrated prediction intervals.
A six-month evaluation window is insufficient to establish stable
operational forecasting performance.

## Experiments 2 and 3: Billing and Stay Regression

### Targets

- Billing Amount.
- LOS_Days.

### Predictor Contract

The following fields are assumed available at admission for this exercise:

- Age
- Gender
- Medical Condition
- Admission Type
- Insurance Provider

This availability assumption requires verification before any real-world use.

### Excluded Predictors

- Name and generated RecordID.
- Hospital and doctor labels.
- Room identifiers.
- Discharge date.
- Length of stay when predicting early billing.
- Test results.
- Medication.
- BillingBand.
- HighBillingDescriptive.
- Billing Amount itself as an input.

Post-admission fields are excluded because their availability at the
prediction time is not established.

### Chronological Partitions

Records are divided by unique admission dates:

- Earliest 60% of dates: training.
- Next 20%: validation.
- Final 20%: test.

These are proportions of unique dates, not guaranteed proportions of rows.

Same-day records remain in the same partition.

Exact split dates and population counts must be recorded from the
executed notebook. The run manifest stores the date cutoffs.

### Preprocessing

Fit preprocessing using training data only:

- Numeric imputation using the training median.
- Numeric standardisation.
- Categorical imputation using the training mode.
- One-hot encoding with unknown-category handling.

The transformations and estimator are combined in a pipeline.

### Models

- Training-mean DummyRegressor baseline.
- Ridge regression with fixed alpha of 10.

The supplied workflow does not perform hyperparameter optimisation.

### Metrics

Report validation and test:

- MAE.
- RMSE.
- R-squared.

Billing errors are in recorded billing units.
Stay errors are in days.

A small positive R-squared does not establish useful prediction.
Compare absolute errors with the baseline and examine stability
between validation and test.

### Result Files

- `data/processed/regression_metrics.csv`
- `data/processed/heldout_regression_predictions.csv`

## Experiment 4: High-Billing Classification

### Target Definition

High billing means Billing Amount is strictly greater than the
training population's 75th-percentile amount.

Calculate the threshold using training records only.
Apply the same threshold to validation and test records.

This differs from the descriptive full-population high-billing flag
used for dashboard exploration.

### Models

- Training-prevalence DummyClassifier baseline.
- Logistic regression with the training-fitted preprocessing pipeline.

### Metrics

Report validation and test:

- High-billing prevalence.
- ROC AUC.
- Average precision.
- Brier score.
- Precision at probability threshold 0.5.
- Recall at probability threshold 0.5.

ROC AUC and average precision require interpretation alongside target
prevalence and baseline results. Lower Brier score is better.

The fixed 0.5 classification threshold may produce no positive
predictions in a low-signal dataset. Zero precision/recall under that
threshold should be reported rather than hidden.

Average precision is not identical to trapezoidal area under a
precision-recall curve; retain the metric's actual name.

### Result Files

- `data/processed/classification_metrics.csv`
- `data/processed/heldout_classification_predictions.csv`

### Interpretation

Predicted probabilities remain experimental.
The workflow does not establish calibration, clinical risk,
or deployable financial prioritisation.

## Results Register

| Experiment | Evidence file | Current documentation status |
|---|---|---|
| Admission forecasting | forecast_metrics.csv | Awaiting actual-data results |
| Billing regression | regression_metrics.csv | Awaiting actual-data results |
| Stay regression | regression_metrics.csv | Awaiting actual-data results |
| High-billing classification | classification_metrics.csv | Awaiting actual-data results |

Replace the status entries after execution and insert the actual
metric tables. Retain the baseline rows alongside candidate models.

## Conclusion Rules

- Report whether each candidate improves on its baseline.
- For regression and classification, examine validation and test stability.
- Do not tune models repeatedly against the final test partition.
- If model selection or tuning is introduced later, use validation data
  and preserve the test set for final evaluation.
- Do not infer useful predictors from feature rankings in a weak model.
- Report negative or inconclusive findings.
- Do not translate synthetic associations into actual treatment,
  staffing, or patient-risk recommendations.

## Unsupported Predictions

This source does not establish:

- Future frequent-admission risk for identifiable patients.
- Clinical recovery probability.
- Medication effectiveness.
- Department-level future demand.
- Reliable provider-specific future loads from sparse labels.
- Actual bed, staffing, or medication-stock requirements.

## Limitations

- Synthetic data may contain little meaningful predictive signal.
- Patient identity is unverified, preventing patient-level partitioning.
- Residual near-duplicates may affect evaluation.
- Predictive feature availability is assumed for the exercise.
- Provider identities and billing semantics are unverified.
- The financial population restriction affects both regression targets.
- Forecast evaluation uses a short historical window.
- Prediction intervals and probability calibration are not established.

## Reproduction

Execute `notebooks/Healthcare_Analysis.ipynb` from top to bottom.

Record the source hash, source version, split dates, training threshold,
population counts, and actual package versions used.

Use the generated run manifest and metrics as the evidence for the
final report and dashboard evaluation page.