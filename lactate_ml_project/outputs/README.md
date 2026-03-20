# Lactate ML Project: Predicting Postoperative Lactate in Neonatal Cardiopulmonary Bypass Surgery for Congenital Heart Defect Repair. 

## Overview

This project evaluates whether early postoperative clinical variables can predict subsequent lactate level i.e., 12th postoperative CICU timepoint (`lactate_3`) in neonates (<28 days old) undergoing cardiopulmonary bypass (CPB) surgery for congenital heart defect repair.

Lactate is a key marker of tissue perfusion and metabolic stress in the cardiac ICU. Understanding its trajectory may support early risk stratification and physiologic assessment.

---

## Problem Statement

Primary: 

Can early postoperative variables predict intermediate lactate (`lactate_3`)?

Secondary:

* Do complex machine learning models outperform simple linear models?
* Do early physiologic variables provide signal beyond prior lactate values?

---

## Dataset

* **Source**: UCSF IRB-approved data derived from APeX chart notes and imported onto and manually curated and cleaned through RedCap database management tool, and deidentified (MRNs and encounterIDs removed) using R. 
* **Population**: Neonates from UCSF Mission Bay Children's Hospital who had CPB surgery for congenital heart defects. 
* **Sample size**: n = 201
* **Outcome**: `lactate_3` or lactate at the 12th Postoperative hour (continuous)

### Features (n = 14)

* Lactate: `lactate_1`, `lactate_2`
* Hemodynamics: `map_1`, `map_2`
* Hematologic: `hct_1`, `hct_2`
* Surgical: `cpb_time`, `x_clamp`, `stat_cat`
* Demographics: `age`, `bw`, `premature`
* Vasoactive support: `vasop_2hr`, `vas_final_cicu_end`

---

## Data Preprocessing

* **Zero values treated as missing**: during data collection, some rows had "0's" placed for "NA's" - missing values, instead of being left blank (a convention standardized for subsequent rows). Since these were physiological variables where 0 is biologically implausible as a value, the 0s for those columns were replaced with NAs. (domain-informed decision)
* **Categorical encoding**:

  * `premature`: Yes → 1, No → 0, Unknown → missing
* **Feature exclusion**:

  * Variables with complete missingness (e.g., `vis`) removed
* **Missing data handling**:

  * Median imputation

---

## Methods

Models implemented:

1. **Linear Regression (baseline)**
2. **Random Forest Regressor**

Evaluation:

* RMSE
* R²

Train/test split:

* 80/20 split
* Fixed random seed

---

## Results

### Full Model (including lactate_2)

| Model             | RMSE | R²   |
| ----------------- | ---- | ---- |
| Linear Regression | 0.91 | 0.63 |
| Random Forest     | 0.98 | 0.57 |

**Finding:**
Linear regression outperformed the nonlinear model.

---

### Sensitivity Analysis (excluding lactate_2)

To assess independent predictive signal:

| Model             | RMSE | R²   |
| ----------------- | ---- | ---- |
| Linear Regression | 1.13 | 0.43 |
| Random Forest     | 1.19 | 0.37 |

---

## Interpretation

* **Strong temporal signal**:

  * Prior lactate (`lactate_2`) dominates prediction
  * Indicates high autocorrelation in lactate trajectory


* **Residual physiologic signal**:
   ##Lactate trajectory demonstrates strong temporal autocorrelation, with prior values dominating prediction.
   ##The persistence of linear model superiority suggests that relationships between early postoperative variables and subsequent lactate remain largely additive rather than nonlinear.

#For sensitivity - lactate 2 (6th postopertaive hour) removed:
  * Even without `lactate_2`, R² ≈ 0.43
  * Suggests contributions from:

    * Hemodynamics (MAP)
    * Surgical burden (CPB time)
    * Vasoactive support

* **Modeling insight**:

  * Linear models consistently outperform Random Forest
  * Relationships appear largely additive rather than nonlinear

---

## Key Takeaways

* Early lactate strongly predicts subsequent lactate
* Other clinical variables still provide meaningful signal
* More complex ML methods did not improve performance
* Domain-informed preprocessing was critical

---

## Trade-offs and Limitations

* Relatively small sample size (n = 201)
* Observational data (no causal inference)
* Temporal proximity of predictors may inflate performance
* Limited feature set (no high-frequency time-series modeling)

---

## Outputs

Figures and tables are saved in:

```text
outputs/
├── figures/
│   ├── pred_vs_actual_linear.png
│   ├── rf_feature_importance.png
│   └── residual_plot.png
│
├── tables/
│   ├── model_performance.csv
│   ├── linear_coefficients.csv
│   └── rf_feature_importance.csv
```

Presentation available at:

```text
outputs/presentation/lactate_prediction_ml_presentation.pdf
```

---

## How to Run

```bash
python -m venv .venv
source .venv/Scripts/activate
pip install pandas numpy scikit-learn matplotlib
jupyter notebook
```

Run the notebook sequentially.

---

## Data Privacy

Dataset is NOT included due to privacy constraints, but can be shown or made available upon request. 
All analyses were performed AFTER clinical data was de-identified.

---

## Future Directions

* Time-series or mixed-effects modeling
* External validation on bigger cohorts
* Integration of additional physiologic signals (e.g., tissue oxygen saturation)

---

## Summary

This study demonstrates that early postoperative lactate trajectory is highly predictive of subsequent lactate levels, along with hemodynamics and hematological factors; and that simple linear models outperform more complex machine learning approaches in this setting.
