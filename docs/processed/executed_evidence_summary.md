# Healthcare analysis evidence summary

Generated from the executed notebook. Synthetic records only; no unique-patient, recovery or deployment claims.

## Cleaning and population

                                                Count
Raw records                                     55500
Raw duplicate copies removed                      534
Additional normalised duplicate copies removed      0
Post-deduplication records                      54966
Invalid date records                                0
Invalid age records                                 0
Negative billing records                          106
Missing billing records                             0

Operational records: 54966; financial records: 54860

## Demographics and conditions

AgeGroup
66+      16096
19-35    13519
51-65    12298
36-50    12167
0-18       886

Gender
MALE      27496
FEMALE    27470

Medical Condition
ARTHRITIS       9218
DIABETES        9216
HYPERTENSION    9151
OBESITY         9146
CANCER          9140
ASTHMA          9095

## Admissions and stay

                Records    MeanLOS  MedianLOS  StayDays
Admission Type                                         
ELECTIVE          18473  15.511178       15.0    286538
EMERGENCY         18102  15.584134       16.0    282104
URGENT            18391  15.403839       15.0    283292

                   Records    MeanLOS  MedianLOS  P90LOS
Medical Condition                                       
ARTHRITIS             9218  15.504231       15.0    27.0
ASTHMA                9095  15.677295       16.0    28.0
CANCER                9140  15.501204       15.0    28.0
DIABETES              9216  15.430664       15.0    27.0
HYPERTENSION          9151  15.436236       15.0    27.0
OBESITY               9146  15.447627       15.0    27.0

## Billing and insurers

                   Records  TotalBilling   MeanBilling  MedianBilling    P90Billing
Medical Condition                                                                  
ARTHRITIS             9207  2.351735e+08  25542.898356   25628.302932  45349.199031
ASTHMA                9077  2.331462e+08  25685.385020   25684.406728  45237.627110
CANCER                9121  2.299032e+08  25205.924207   24990.114131  44956.536466
DIABETES              9197  2.364947e+08  25714.326349   25681.008034  45156.865687
HYPERTENSION          9131  2.333869e+08  25559.844868   25337.536074  45417.314244
OBESITY               9127  2.360171e+08  25859.215373   26200.514545  44962.962269

                    AdmissionRecords
Insurance Provider                  
CIGNA                          11139
MEDICARE                       11039
UNITEDHEALTHCARE               11014
BLUE CROSS                     10952
AETNA                          10822

## Test results and medication labels

                   Records  NormalRecords  NormalResultShare
Medical Condition                                           
ARTHRITIS             9218           3000           0.325450
ASTHMA                9095           3116           0.342606
CANCER                9140           3018           0.330197
DIABETES              9216           3061           0.332140
HYPERTENSION          9151           3106           0.339416
OBESITY               9146           3030           0.331292

Medication         ASPIRIN  IBUPROFEN  LIPITOR  PARACETAMOL  PENICILLIN
Medical Condition                                                      
ARTHRITIS             1901       1805     1810         1858        1844
ASTHMA                1781       1802     1814         1870        1828
CANCER                1768       1862     1904         1829        1777
DIABETES              1836       1846     1875         1794        1865
HYPERTENSION          1845       1874     1823         1839        1770
OBESITY               1853       1834     1812         1775        1872

## Resources

                                           Records  StayDays    MeanLOS  StayDaysShare
AgeGroup Medical Condition Admission Type                                             
66+      CANCER            EMERGENCY           916     14864  16.227074       0.017447
         ARTHRITIS         URGENT              946     14811  15.656448       0.017385
         HYPERTENSION      URGENT              931     14345  15.408163       0.016838
                           ELECTIVE            930     14334  15.412903       0.016825
         ARTHRITIS         EMERGENCY           915     14326  15.656831       0.016816
         ASTHMA            URGENT              915     14293  15.620765       0.016777
         DIABETES          URGENT              930     14197  15.265591       0.016664
         ASTHMA            EMERGENCY           908     14051  15.474670       0.016493
         OBESITY           ELECTIVE            866     13944  16.101617       0.016367
                           EMERGENCY           889     13883  15.616423       0.016296

## Forecast evaluation

      Model       MAE      RMSE
   Baseline 23.995048 27.739951
LinearTrend 19.278782 25.823346

## Regression evaluation

        Target        Model      Split          MAE         RMSE        R2
Billing Amount TrainingMean Validation 12171.837575 14094.934565 -0.000089
Billing Amount TrainingMean       Test 12242.232334 14152.736130 -0.000014
Billing Amount        Ridge Validation 12178.599143 14104.349760 -0.001426
Billing Amount        Ridge       Test 12243.301945 14157.978688 -0.000755
      LOS_Days TrainingMean Validation     7.480754     8.632280 -0.000026
      LOS_Days TrainingMean       Test     7.629347     8.769432 -0.000055
      LOS_Days        Ridge Validation     7.478737     8.631798  0.000086
      LOS_Days        Ridge       Test     7.631200     8.773509 -0.000985

## Classification evaluation

             Model      Split  HighBillingPrevalence  ROC_AUC  AveragePrecision    Brier  Precision_0.5  Recall_0.5
TrainingPrevalence Validation               0.244842 0.500000          0.244842 0.184921            0.0         0.0
TrainingPrevalence       Test               0.245882 0.500000          0.245882 0.185441            0.0         0.0
          Logistic Validation               0.244842 0.499686          0.243965 0.184976            0.0         0.0
          Logistic       Test               0.245882 0.500090          0.245971 0.185516            0.0         0.0

## Answerability

Question                RequestedAnalysis                  Status                                                      EvidenceOrLimitation
      1a                 Age distribution             Descriptive                                    Age histogram and corrected age groups
      1b              Gender distribution             Descriptive                                Admission-record counts; not unique people
      1c    Blood types within conditions             Descriptive                                   Within-condition percentages and counts
      2a           Most common conditions             Descriptive                                          Condition counts; not prevalence
      2b                Conditions by age             Descriptive                                                Counts plus within-age mix
      2c Blood type-condition association             Exploratory                                  Chi-square and Cramers V; synthetic-only
      3a             Admissions over time             Descriptive                                  Year-month counts and observed-day rates
      3b       Most common admission type             Descriptive                                                         Counts and shares
      3c            LOS by condition/type             Descriptive                                             Mean, median and denominators
      4a             Billing by condition             Descriptive                            Nonnegative amounts and eligible-record counts
      4b             Insurer record count             Descriptive                               Admission-record count, not unique patients
      4c       Admission type and billing             Descriptive                                 Distribution summaries, not causal effect
      5a        Highest hospital caseload             Descriptive                                  Top labels by count; identity unverified
      5b          Highest doctor caseload             Descriptive                                  Top labels by count; identity unverified
      5c     Provider outcome differences              Restricted             Normal-result shares, count screen and intervals; not quality
      6a         Test-result distribution             Descriptive                                                       Recorded categories
      6b            Highest recovery rate Not available as worded                        Normal-result share reported; no recovery variable
      6c         Medication per condition             Descriptive                      Frequency of synthetic labels, no prescribing advice
      7a                     Peak periods             Descriptive                   Chronological counts and complete-month/day comparisons
      7b    Groups needing most resources              Proxy only                     Record counts/stay-days; no nursing or cost intensity
      7c     Room allocation and outcomes              Restricted              Hospital-room groups; no causal or ordinal room-number claim
      8a             Projected admissions            Experimental      Six-month rolling backtest vs baseline; historical-origin projection
      8b    Conditions likely to increase         Not established         Historical rates; separate condition forecast validation required
      8c   Frequent future admission risk           Not available                   No verified patient identifier or longitudinal outcomes
      8d              Longer future stays            Experimental                   Admission-time LOS regression vs training-mean baseline
      8e    Early high billing indicators            Experimental Training-threshold classifier and billing regression, baseline comparison
      8f Future hospital/department loads         Not established                    Sparse/unverified provider labels; no department field
      8g                Resource planning            Illustrative     Stay-day/census scenarios; no actual capacity or medication-dose data

## Conclusion rules

Claim predictor/forecast skill only if validation and test errors improve materially on baselines. Do not turn random/synthetic differences into real resource or treatment recommendations. Names cannot establish frequent admission risk; Normal is not recovery.