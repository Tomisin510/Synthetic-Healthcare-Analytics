# Healthcare Analytics Demonstration

## Purpose

Demonstrate admission-date and category filtering, operational versus
financial populations, and honest interpretation of synthetic healthcare
analysis.

Record the demonstration only after the revised report reconciles
with the executed notebook.

## Report

Open:

`report/Healthcare_Admissions_Billing.pbix`

The PDF report is static. Use Power BI Desktop for interaction.

## Suggested 90-Second Sequence

| Time | Action |
|---|---|
| 0–12 seconds | Show Admissions Overview with full observed dates and all categories selected |
| 12–25 seconds | Select an admission type and change the date range |
| 25–38 seconds | Open Demographics and Conditions; select an age group |
| 38–52 seconds | Open Billing and Insurance; show eligible and negative-billing record counts |
| 52–65 seconds | Open Stay and Provider Labels; show counts beside descriptive measures |
| 65–80 seconds | Open Predictive Evaluation and Limits; compare candidate results with baselines |
| 80–90 seconds | Clear selections and restore the overview |

If the report uses synchronised slicers, ensure the selected filters
remain visible on every affected page.

Predictive metric tables describe a fixed evaluation run. Unless
explicitly designed otherwise, dashboard slicers do not retrain models
or recalculate their evaluation.

## Narration

This project explores synthetic healthcare admission records,
recorded billing, and stay duration.

The overview counts admission records rather than verified unique
patients. Date and admission-type filters update the descriptive views.

The demographic page uses correctly labelled age groups and compares
condition mix alongside record counts.

The billing page separates operational records from financial eligibility.
Otherwise valid records with negative billing remain in admission analysis,
while nonnegative-billing measures exclude their amounts.

Provider displays show recorded labels and case counts, not clinical
quality. Normal test results are not labelled recovery.

The evaluation page compares simple predictive models with transparent
baselines. Its results are fixed experimental evidence, not individual
clinical-risk scores.

Clearing selections restores the report's baseline view.

## Preparation

- Confirm the revised source files are loaded.
- Reconcile unfiltered totals with the notebook.
- Restore full observed admission coverage.
- Clear unintended gender, billing-band, and admission-type selections.
- Collapse editing panes and use Fit to page.
- Keep navigation and active filters readable.
- Pause briefly after each selection.
- Preview the final recording for readable text and clear audio.

## Video

Suggested filename:

`Healthcare_Analytics_Demo.mp4`

Add a relative or externally hosted video link after recording.

## Interpretation

The demonstration presents an educational synthetic-data project.

It does not establish recovery, treatment effectiveness,
unique-patient readmission risk, provider quality, or deployable
hospital resource forecasts.