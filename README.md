# Preoperative Machine Learning Prediction of Pain Improvement after CABG

Analysis code for the article:

> Mara'Beh R.A., Mara'Beh S., Sawalha O. *Machine Learning Prediction of Postoperative Pain Improvement
> Following Coronary Artery Bypass Grafting Using Preoperative Clinical, Psychosocial, Fatigue, and
> Functional Factors.* (Submitted to BMC Medical Informatics and Decision Making.)

The notebook trains and compares six classifiers (Logistic Regression, SVC, KNN, Decision Tree,
Random Forest, Gradient Boosting) to predict whether a patient's pain-severity category improves
two to three weeks after coronary artery bypass grafting, using 37 preoperative variables only.
It also contains every robustness analysis reported in the article: bootstrap confidence intervals,
SHAP and permutation importance, calibration and decision curves, leave-one-hospital-out validation,
pain-only benchmarks, nested cross-validation, statistical model comparisons, threshold analysis and
an alternative outcome.

## Repository contents

| File | Description |
|---|---|
| `PainImprovement_CABG_ML.ipynb` | Fully commented analysis notebook (20 sections, no stored outputs). |
| `feature_dictionary.csv` | Each predictor's raw column name, display name, domain, instrument, type, encoding and observed range; outcome and grouping columns. |
| `input_template.xlsx` | Empty spreadsheet with every column the notebook reads, in the required format. |
| `requirements.txt` | Python packages and the exact versions used for the article. |
| `LICENSE` | MIT license for the code. |

## Data availability

**The patient data are not included in this repository.** They come from a prospective cohort of
CABG patients in six hospitals in the West Bank, Palestine, and are available from the authors on
reasonable request, subject to the data-sharing policies of the original study.

To run the notebook, place the dataset in the notebook's folder as `Data.xlsx`, or set `DATA_PATH` in
Section 1. A dataset from another setting can be analysed in the same way if it has the columns and
codings listed in `feature_dictionary.csv`. The function `evaluate_on_external_dataset()` (Section 20)
applies the trained model to such an external dataset.

## How to run

```bash
python -m venv venv && source venv/bin/activate      # optional: isolated environment
pip install -r requirements.txt
jupyter notebook PainImprovement_CABG_ML.ipynb
```

Then choose *Run All*. Settings at the top of the notebook:

* `RANDOM_STATE = 42` – fixed seed for every random step, so results are reproducible.
* `SAVE_OUTPUTS = True` – writes result tables (`.xlsx`) and figures (`.png`) to the working folder.
* `RUN_SLOW_ANALYSES = True` – Sections 18–19 (nested cross-validation, permutation tests, learning
  curve, alternative outcome) take about 30–60 minutes on a laptop; set to `False` to skip them.

Kernel SHAP values are Monte Carlo estimates and can vary slightly between runs; all other results are
deterministic for the stated package versions.

## Analysis pipeline and leakage controls

1. Complete-case analysis (8 of 200 records missing the Charlson index; decided on predictor
   missingness only). A sensitivity analysis imputes these values inside the cross-validation folds.
2. Categorical predictors are encoded with fixed mappings that do not depend on the data.
3. Outcome: postoperative minus preoperative pain category; *Improved* if negative, otherwise *Not Improved*.
4. Stratified 70/30 train/test split; the test set is never used for tuning.
5. Standardisation is fitted inside scikit-learn pipelines, on training folds only.
6. Exhaustive `GridSearchCV` (stratified 5-fold, weighted F1) for each model; all 37 pre-specified
   predictors are used (no data-driven feature selection).
7. Optimism from choosing the final algorithm is quantified by nested cross-validation in which algorithm
   selection is repeated inside every training fold.

## Privacy

Cells that would display individual patient records have been omitted. If you run the notebook on
the real data, **clear all outputs before sharing or committing it**
(*Kernel → Restart & Clear Output*). The `.gitignore` file prevents data files and generated outputs
from being committed by accident.

## Citation

If you use this code, please cite the article above.

## Contact

Saed Mara'Beh – smarabeh@qu.edu.qa
