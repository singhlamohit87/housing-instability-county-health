# housing-instability-county-health
**ORCID:** 0009-0009-1518-4598  

---

## Overview

Prior county-level machine learning work established that severe housing instability outranks poverty and unemployment as a predictor of Medicare opioid cost burden across 3,100 U.S. counties (2013–2023). This repository tests whether that finding generalizes to three additional county health burden outcomes using the same analytical pipeline and SDOH feature set.

**Three outcomes analyzed:**
- Premature death rate (years of potential life lost per 100,000 population)
- Poor mental health days (average mentally unhealthy days per month)
- Preventable hospital stays (ambulatory care-sensitive hospitalizations per 100,000)

**Key finding:** Housing instability does not generalize as the dominant SDOH predictor across county health burden indicators. Child poverty remains the primary correlate for premature death (ρ=0.819) and mental health days (ρ=0.625), while housing instability is not significantly associated with preventable hospital stays (ρ=−0.005, p=0.453). After controlling for poverty, housing instability shows negative partial correlations with premature death (ρ=−0.128) and preventable hospitalizations (ρ=−0.093), suggesting outcome-specificity rather than a universal structural health burden signal.

---

## Data Sources

All data are publicly available and require no registration or access approval.

| Dataset | Source | Years | URL |
|---|---|---|---|
| Medicare Part D Geography Prescribing Data | Centers for Medicare & Medicaid Services (CMS) | 2013–2023 | https://data.cms.gov/ |
| County Health Rankings | University of Wisconsin Population Health Institute | 2014–2024 | https://www.countyhealthrankings.org/ |

---

## Repository Structure

```
├── notebooks/
│   └── housing_instability_multi_outcome.ipynb   # Full analysis notebook
├── results/
│   ├── spearman_correlations.csv                 # Spearman rho + bootstrap 95% CIs
│   ├── partial_correlations.csv                  # Partial Spearman (housing|poverty)
│   ├── ols_coefficients.csv                      # OLS regression coefficients
│   └── rf_performance.csv                        # Random Forest holdout performance
├── figures/
│   ├── fig1_spearman_correlations.png            # Grouped bar chart: rho by predictor/outcome
│   ├── fig2_scatter_housing_vs_outcomes.png      # Scatter: housing vs each outcome
│   └── fig3_ols_coefficients.png                 # OLS coefficients across outcomes
├── LICENSE
└── README.md
```

---

## Methods Summary

**Predictors:** 12 SDOH features from County Health Rankings including housing instability, poverty, unemployment, income inequality, provider rates, and behavioral health indicators.

**Temporal validation:** Training set 2013–2020 / Test set 2021–2023 (completely held out — same design used in the original opioid cost study).

**Statistical analysis:**
- Spearman rank correlations with bootstrap 95% confidence intervals (n=1,000 iterations)
- Partial Spearman correlations (rank-based residualization controlling for poverty)
- OLS regression with holdout evaluation
- Random Forest regression with holdout evaluation

---

## Model Performance (Temporal Holdout 2021–2023)

| Outcome | n Train | n Test | RF R² | OLS R² |
|---|---|---|---|---|
| Premature Death Rate | 65,052 | 24,399 | 0.966 | 0.800 |
| Poor Mental Health Days | 66,576 | 24,966 | 0.928 | 0.568 |
| Preventable Hospital Stays | 66,288 | 24,858 | 0.783 | 0.256 |

---

## Key Results

**Spearman correlations (2021–2023 test set):**

| Predictor | Premature Death | Mental Health Days | Prev. Hosp. Stays |
|---|---|---|---|
| Housing Instability | ρ=0.121 [0.108, 0.132] | ρ=0.179 [0.169, 0.190] | ρ=−0.005 [−0.009, 0.017] ns |
| Child Poverty | ρ=0.819 [0.814, 0.823] | ρ=0.625 [0.605, 0.620] | ρ=0.371 [0.362, 0.382] |
| Unemployment | ρ=0.400 [0.389, 0.409] | ρ=0.431 [0.421, 0.442] | ρ=0.190 [0.179, 0.201] |

Values shown as ρ [95% bootstrap CI]. ns = not significant (p=0.453).

**Partial Spearman (housing instability controlling for poverty):**

| Outcome | Partial ρ | p-value |
|---|---|---|
| Premature Death Rate | −0.128 | <0.001 |
| Poor Mental Health Days | +0.046 | <0.001 |
| Preventable Hospital Stays | −0.093 | <0.001 |

---

## Replication

**Requirements:**
```
pip install pandas numpy scipy scikit-learn matplotlib seaborn
```

**Steps:**
1. Download `geo_2023.csv` from CMS (Medicare Part D Geography, 2013–2023)
2. Download `sdoh_2024.csv` from County Health Rankings (2024 release)
3. Place both files in a `data/` folder
4. Open and run `notebooks/housing_instability_multi_outcome.ipynb`
5. Results save to `results/` and figures to `figures/`

---

## Related Work

County-level opioid cost burden analysis using the same SDOH pipeline:  
https://github.com/singhlamohit87/opioid-cost-prediction-pbm-sdoh

---

## License

MIT License. See [LICENSE](LICENSE) for details.

## Ethics Statement

This study uses exclusively publicly available, de-identified federal administrative and survey data. No human subjects were directly involved. Exempt from IRB review under 45 CFR 46.101(b).
