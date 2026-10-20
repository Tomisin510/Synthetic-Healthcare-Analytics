# DAX Measures and Model Documentation

**Project:** Synthetic Healthcare Analytics  
**Author:** Tomisin Obijole

## Implementation Status

These definitions describe the revised analytical design.

Compare them with the implemented Power BI measures before treating
this document as documentation of the completed report.

## Data Model

Import `data/processed/healthcare_records_v2.csv` as FactAdmissions.

Create an active relationship:

| One side | Many side | Configuration |
|---|---|---|
| DimDate[Date] | FactAdmissions[AdmissionDate] | One-to-many, single direction |

All measures use admission-date filtering by default.

If discharge-date analysis is added, use a separate role-playing date
dimension or an inactive discharge relationship with an explicitly
named measure.

## Data Types

| Field | Type |
|---|---|
| RecordID | Text |
| Age | Whole Number |
| AgeGroup | Text |
| Hospital | Text |
| Doctor | Text |
| Room Number | Text identifier |
| AdmissionDate | Date |
| DischargeDate | Date |
| LOS_Days | Whole Number |
| Billing Amount | Decimal Number |
| BillingEligible | True/False |
| NegativeBilling | True/False |
| HighBillingDescriptive | Nullable True/False |

Do not sum identifiers or display billing with an unverified
currency symbol.

## Metric Rules

- Admission Records is not a unique-patient count.
- Normal Test Result Share is not recovery.
- Recorded Stay Days is an admission-cohort duration sum, not occupancy.
- Nonnegative billing is not net revenue, cash received, or verified cost.
- Observed high billing is retrospective, not predicted risk.
- Measures respond to active report filters.
- KEEPFILTERS intersects metric eligibility with existing selections.

## 1. Admission Records

```dax
Admission Records =
COUNTROWS ( FactAdmissions )
```

**Format:** Whole number.

Counts the selected operational records.

## 2. Mean Length of Stay

```dax
Mean Length of Stay =
AVERAGE ( FactAdmissions[LOS_Days] )
```

**Format:** Decimal, two places.  
**Unit:** Days.

## 3. Median Length of Stay

```dax
Median Length of Stay =
MEDIAN ( FactAdmissions[LOS_Days] )
```

**Format:** Decimal, one or two places.  
**Unit:** Days.

## 4. Recorded Stay Days

```dax
Recorded Stay Days =
SUM ( FactAdmissions[LOS_Days] )
```

**Format:** Whole number.

For date-filtered cohorts, this includes the full duration of records
admitted during the selection, even when discharge occurs later.

It is not the number of occupied beds within the selected calendar period.

## 5. Negative Billing Records

```dax
Negative Billing Records =
CALCULATE (
    [Admission Records],
    KEEPFILTERS ( FactAdmissions[NegativeBilling] = TRUE () )
)
```

**Format:** Whole number.

These records remain operationally included when otherwise valid.

## 6. Billing Eligible Records

```dax
Billing Eligible Records =
CALCULATE (
    [Admission Records],
    KEEPFILTERS ( FactAdmissions[BillingEligible] = TRUE () )
)
```

**Format:** Whole number.

Counts records eligible for nonnegative financial summaries.

## 7. Nonnegative Recorded Billing

```dax
Nonnegative Recorded Billing =
CALCULATE (
    SUM ( FactAdmissions[Billing Amount] ),
    KEEPFILTERS ( FactAdmissions[BillingEligible] = TRUE () )
)
```

**Format:** Decimal with thousands separators.

Use no currency symbol until the source currency is verified.

## 8. Mean Nonnegative Billing

```dax
Mean Nonnegative Billing =
DIVIDE (
    [Nonnegative Recorded Billing],
    [Billing Eligible Records]
)
```

**Format:** Decimal, two places.

The numerator and denominator use the same eligible population.

## 9. Median Nonnegative Billing

```dax
Median Nonnegative Billing =
CALCULATE (
    MEDIAN ( FactAdmissions[Billing Amount] ),
    KEEPFILTERS ( FactAdmissions[BillingEligible] = TRUE () )
)
```

**Format:** Decimal, two places.

## 10. Normal Test Records

```dax
Normal Test Records =
CALCULATE (
    [Admission Records],
    KEEPFILTERS ( FactAdmissions[Test Results] = "NORMAL" )
)
```

**Format:** Whole number.

The revised pipeline standardises result labels to uppercase.

## 11. Recognised Test Records

```dax
Recognised Test Records =
CALCULATE (
    [Admission Records],
    KEEPFILTERS (
        FactAdmissions[Test Results]
            IN { "NORMAL", "ABNORMAL", "INCONCLUSIVE" }
    )
)
```

**Format:** Whole number.

Excludes missing or unrecognised result categories from the
test-share denominator.

## 12. Normal Test Result Share

```dax
Normal Test Result Share =
DIVIDE (
    [Normal Test Records],
    [Recognised Test Records]
)
```

**Format:** Percentage, one decimal.

This is not clinical recovery or treatment success.

If Test Results is itself filtered, the measure describes the remaining
recognised categories. Selecting only NORMAL can therefore produce 100%.

The notebook's overall snapshot currently divides by all operational
records. These denominators agree only when every operational record
has a recognised test result. Check the category audit and reconcile
or explicitly document any difference.

## 13. Observed High Billing Records

```dax
Observed High Billing Records =
CALCULATE (
    [Admission Records],
    KEEPFILTERS ( FactAdmissions[BillingEligible] = TRUE () ),
    KEEPFILTERS ( FactAdmissions[HighBillingDescriptive] = TRUE () )
)
```

**Format:** Whole number.

This uses the descriptive threshold calculated across the full financial
population. The flag does not recalculate when report filters change.

It is different from the training-only target used in model evaluation.

## 14. Observed High Billing Share

```dax
Observed High Billing Share =
DIVIDE (
    [Observed High Billing Records],
    [Billing Eligible Records]
)
```

**Format:** Percentage, one decimal.

The share changes with selection, while the underlying descriptive
threshold remains fixed until the source analysis is rerun.

## Date Table

```dax
DimDate =
VAR FirstDate =
    MIN ( FactAdmissions[AdmissionDate] )
VAR LastDate =
    MAX ( FactAdmissions[DischargeDate] )
RETURN
    ADDCOLUMNS (
        CALENDAR (
            DATE ( YEAR ( FirstDate ), 1, 1 ),
            DATE ( YEAR ( LastDate ), 12, 31 )
        ),
        "Year", YEAR ( [Date] ),
        "YearMonth", FORMAT ( [Date], "yyyy-MM" ),
        "YearMonthSort", YEAR ( [Date] ) * 100 + MONTH ( [Date] )
    )
```

Configuration:

1. Mark DimDate as a date table using Date.
2. Sort YearMonth by YearMonthSort.
3. Relate Date actively to AdmissionDate.
4. Use YearMonth, not individual admission dates, for the monthly chart.
5. Set the default date selection to actual observed admission coverage.

The generated monthly analysis flags partial boundary months.
Do not interpret those totals as comparable full months.

## Age-Group Sort Column

Create in FactAdmissions:

```dax
AgeGroupSort =
SWITCH (
    FactAdmissions[AgeGroup],
    "0-18", 1,
    "19-35", 2,
    "36-50", 3,
    "51-65", 4,
    "66+", 5,
    99
)
```

Select AgeGroup and sort it by AgeGroupSort.

## Provider Displays

- Select Top N provider labels by Admission Records.
- Show record counts beside result shares.
- Apply a documented minimum-record screen for share comparisons.
- Treat provider strings as recorded labels, not verified identities.
- Do not describe caseload or Normal-result share as clinical quality.

## Predictive Tables

Forecast, regression, and classification outputs are evaluation evidence.

Keep them separate from admission-detail metrics unless a specific,
validated relationship is designed.

A retrospective high-billing flag must not be relabelled predicted risk.

## Validation

Use:

- `data/processed/cleaning_audit.csv`
- `docs/run_manifest.json`
- `docs/executed_evidence_summary.md`

Clear all date, gender, admission-type, insurer, condition, and billing
selections before comparing full-period totals.

Validate that:

1. Admission Records matches the operational population.
2. Billing Eligible Records matches the financial population.
3. Financial amounts exclude negative and missing billing.
4. Negative-billing records remain in operational summaries.
5. Age-group counts use corrected endpoints.
6. Normal-result denominators are explicitly reconciled.
7. Partial-month coverage is visible.