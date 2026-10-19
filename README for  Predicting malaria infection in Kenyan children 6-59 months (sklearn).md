# Predicting Malaria Infection in Kenyan Children Aged 6–59 Months

A reproducible machine-learning demonstration for predicting malaria infection among children aged 6–59 months using **synthetic, KMIS-like survey data**. The project focuses not only on model performance, but also on validation design, cluster leakage, class imbalance, calibration, survey-weighted evaluation, and predictor leakage.

> **Important:** The dataset is synthetic. It is not real Kenya Malaria Indicator Survey (KMIS) data, and the results are illustrative rather than evidence about actual malaria risk in Kenya. This project is for learning and methodological demonstration—not clinical or public-health decision-making.

## Project files

Keep the following files together in the same GitHub repository:

| File | Purpose |
|---|---|
| `Predicting malaria infection in Kenyan children 6-59 months (sklearn).ipynb` | Main notebook containing data checks, preprocessing, model fitting, cross-validation, evaluation, and figures. |
| `synthetic_kmis.csv` | Synthetic child-level dataset used by the notebook. |
| `malaria_prediction_cv_results.xlsx` | Excel workbook containing the cross-validation summary table exported by the notebook. |
| `validation_figure.png` | Figure showing precision–recall performance, calibration, and naive versus cluster-grouped validation. |
| `README.md` | Project overview, instructions, and methodological notes. |

The notebook expects the dataset to be named `synthetic_kmis.csv` and located in the same directory as the notebook. The Excel results and validation figure are generated when the relevant notebook cells run.

## Research question

How well can selected machine-learning models discriminate between malaria-positive and malaria-negative outcomes in a synthetic survey population of Kenyan children aged 6–59 months, when validation accounts for clustering?

The outcome is `malaria_pos`, a binary indicator of malaria positivity. The notebook uses predictors such as age in months, household characteristics, residence, geographic zone, maternal education, housing and water-source characteristics, sex, and mosquito-net use. The variable `anaemia` is deliberately excluded from the main predictor set because the demonstration treats it as a possible consequence of malaria; including it can create target leakage and produce misleadingly optimistic performance.

## Methods and workflow

1. **Data audit:** inspect the number of children and clusters, outcome prevalence, prevalence by zone, missingness, and cluster sizes.
2. **Preprocessing:** impute missing numeric and categorical values, scale numeric variables, and one-hot encode categorical variables. Preprocessing is placed inside scikit-learn pipelines so that it is fitted within the training data for each fold.
3. **Models:** compare a prior-probability dummy classifier, L2-regularized logistic regression, and histogram-based gradient boosting.
4. **Validation:** use stratified, cluster-grouped cross-validation to keep observations from the same cluster together. The notebook also compares naive stratified cross-validation with cluster-grouped validation to demonstrate how ignoring cluster structure can affect estimated performance. Hyperparameters are tuned using an inner grouped cross-validation loop for the tuned models.
5. **Evaluation:** report ROC-AUC, precision–recall average precision (PR-AUC/AP), Brier score, calibration slope, and the share of positive cases captured among the highest predicted-risk 20%. Survey-weighted ROC-AUC and PR-AUC are also calculated.
6. **Leakage demonstration:** compare grouped-validation performance with and without the deliberately excluded `anaemia` variable.
7. **Interpretation:** inspect logistic-regression odds ratios as associations, and use permutation importance on held-out clusters to assess the contribution of predictors to PR-AUC.

## Why cluster-aware validation matters

Children sampled from the same village or enumeration area may share environmental, socioeconomic, and health-service characteristics. If observations from one cluster appear in both training and test folds, a model may benefit from information related to that shared cluster. Its performance may then look better than it would when applied to entirely new clusters.

The notebook therefore uses `StratifiedGroupKFold` for its primary validation and compares results with ordinary stratified folds. The comparison is a methodological demonstration; the size and direction of any difference depend on the simulated data and should be assessed from the exported results rather than assumed in advance.

## Metrics in context

- **ROC-AUC:** discrimination across classification thresholds.
- **PR-AUC / average precision:** precision–recall performance, particularly informative when the positive class is uncommon. Compare it with the outcome prevalence as a no-skill reference.
- **Brier score:** mean squared error of predicted probabilities; lower is better.
- **Calibration slope:** assesses whether predicted risks are too extreme or not extreme enough. Interpret it alongside a calibration plot and other calibration measures.
- **Top-20% capture:** proportion of positive cases found among the 20% of observations with the highest predicted risks. This is a ranking/capacity metric, not a recommendation to screen or intervene using a fixed threshold.
- **Survey-weighted metrics:** account for the synthetic survey weights in selected performance measures. They do not, by themselves, make the data or analysis representative of Kenya's population.

No single metric establishes that a model is useful. Discrimination, calibration, data quality, validation design, practical consequences, and the intended use must all be considered.

## Requirements

The notebook uses Python and common data-science packages, including:

- `numpy`
- `pandas`
- `matplotlib`
- `scikit-learn`
- `openpyxl` (for exporting the Excel workbook)

Install the dependencies in your environment:

```bash
pip install numpy pandas matplotlib scikit-learn openpyxl jupyter
```

## How to run

1. Clone or download this repository.
2. Confirm that `synthetic_kmis.csv` is in the same folder as the notebook.
3. Install the dependencies listed above.
4. Open Jupyter Notebook or JupyterLab.
5. Open `Predicting malaria infection in Kenyan children 6-59 months (sklearn).ipynb`.
6. Run the notebook from top to bottom.

The notebook reads `synthetic_kmis.csv` and writes `malaria_prediction_cv_results.xlsx` and `validation_figure.png` to the working directory. Cross-validation and hyperparameter tuning may take some time depending on your computer.

## Reproducibility

The notebook defines a random seed (`2026`) and uses repeated cross-validation to reduce reliance on a single fold assignment. Results may still vary with package versions, environment settings, or code changes. For a formal analysis, record package versions and the data-generation process, and rerun the complete pipeline from a clean environment.

## Limitations

- **Synthetic data:** simulated patterns cannot establish real-world prevalence, risk factors, or predictive performance.
- **Not official KMIS data:** the KMIS-like label describes the survey-inspired structure only; it does not imply access to or analysis of official survey microdata.
- **No external validation:** the demonstration does not establish performance in a separate real population, time period, or health system.
- **Survey design:** using survey weights for selected metrics is not equivalent to conducting a complete complex-survey analysis. The notebook's cluster-aware cross-validation is a validation strategy, not a full survey-design estimator.
- **Calibration:** class weighting can affect the calibration of predicted probabilities. Review the calibration plot and calibration statistics before interpreting probabilities as absolute risks.
- **Associations are not causal effects:** logistic-regression odds ratios and feature-importance values do not establish causation.
- **Operational use:** no clinical, screening, or resource-allocation decision should be based on this synthetic demonstration.

## Skills demonstrated

Python data analysis; pandas; scikit-learn pipelines; missing-data imputation; categorical encoding; logistic regression; gradient boosting; nested cross-validation; grouped validation; model evaluation under class imbalance; calibration assessment; survey-weighted metrics; leakage detection; permutation importance; reproducible analytical workflows; and communication of methodological limitations.

## Suggested citation

If you use or adapt this repository, cite it as an educational project using synthetic data. Add your name, repository URL, and date/version when publishing a formal citation.

---

**Disclaimer:** This is an educational modelling exercise. It is not medical advice, a validated malaria screening tool, or an analysis of actual Kenyan children.
