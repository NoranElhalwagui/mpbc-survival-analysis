# Personalized Treatment Decision Support for Metaplastic Breast Cancer Using ML-Based Survival Modeling on SEER Data

Code for a survival analysis study of **metaplastic breast cancer (MpBC)**, a rare and aggressive breast cancer subtype, using the SEER registry. We build what we believe is the largest MpBC survival cohort to date (4,536 patients from over 1.28 million breast cancer records) and compare five survival models: Cox Proportional Hazards, Random Survival Forest, Gradient Boosted Survival (XGBoost), CatBoost Survival, and DeepSurv.

> **Research use only.** This project is an academic study. It is not a medical device and must not be used to make clinical decisions.

## Key results

Held-out test set (30%, n = 1,361):

| Model | C-index | 5-year AUC |
|---|---|---|
| Cox Proportional Hazards (baseline) | 0.685 | n/a |
| CatBoost Survival | 0.719 | n/a |
| DeepSurv | 0.740 | n/a |
| **Random Survival Forest** | **0.788** | n/a |
| Gradient Boosted Survival (XGBoost) | n/a | **0.812** |

- Random Survival Forest had the best overall discrimination (C-index 0.788), above the best published MpBC Cox nomogram (0.769).
- XGBoost had the strongest five-year time-dependent AUC (0.812).
- Machine learning models outperformed Cox regression, but the gains are incremental. Cox remains a meaningful baseline.
- The most influential prognostic factors were stage, surgery, metastasis, tumor grade, and receptor status.

Models were evaluated with the concordance index, time-dependent AUC at 1, 3 and 5 years (IPCW), and the Integrated Brier Score.

## Data

This repository **does not include any SEER data**. SEER data is distributed under a data use agreement, so you must request access yourself through the [SEER program](https://seer.cancer.gov/data/) and export the breast cancer dataset (2000–2021, 17 registries).

### Cohort construction

| Criterion | N retained | N removed |
|---|---|---|
| Raw SEER breast records | 1,281,629 | n/a |
| Female sex | 1,281,629 | 0 |
| Malignant behavior only | 1,281,629 | 0 |
| First primary tumor | 1,059,695 | 221,934 |
| Known survival time (> 0) | 1,017,288 | 42,407 |
| Known vital status | 1,017,288 | 0 |
| MpBC histology (ICD-O-3 8570–8575) | 4,536 | 1,012,752 |

Final cohort: 4,536 patients, median follow-up 49 months, event (death) rate 25.9%.

Treatment distribution: surgery + chemotherapy + radiation (35.1%), surgery + chemotherapy (25.1%), surgery only (22.9%), surgery + radiation (11.0%), no surgery (5.9%).

## Method overview

**Preprocessing.** A 17-step pipeline produces a matrix of 25 features with no missing values. It harmonizes grade, tumor size, and stage across SEER coding eras, replaces SEER sentinel codes, builds a distant-metastasis flag and a node ratio, derives triple-negative status, and encodes treatment into four arms plus a no-surgery group. Survival-derived variables are excluded to prevent leakage, and all transformations are fit on the training set only.

**Models.**

| Model | Configuration |
|---|---|
| Cox PH | Statistical baseline; proportional hazards checked with Schoenfeld residuals |
| Random Survival Forest | 500 trees, minimum node size 15, log-rank splitting, permutation importance |
| Gradient Boosted Survival (XGBoost) | Cox partial likelihood loss, 5-fold cross-validation on C-index |
| CatBoost Survival | Native categorical handling with ordered target statistics |
| DeepSurv | `pycox`, 3 layers (128-64-32), ReLU, batch normalization, dropout 0.3 |

**Experimental design.** Stratified 70/30 train/test split on event status (train n = 3,175, test n = 1,361, seed 42).

**Individualized prediction.** The XGBoost model is extended to predict an individual patient's five-year mortality risk from clinical and treatment variables. Outputs are associational and do not estimate causal treatment effects.

## Repository structure

<!-- TODO: replace with your actual files and folders -->

| Step | What it does | File |
|---|---|---|
| 1 | Cohort construction from raw SEER export | TODO |
| 2 | 17-step preprocessing and feature engineering | TODO |
| 3 | Model training and evaluation | TODO |
| 4 | Risk stratification and Kaplan-Meier plots | TODO |
| 5 | Individualized prediction / decision-support tool | TODO |

## Getting started

```bash
git clone https://github.com/NoranElhalwagui/<repo-name>.git
cd <repo-name>
pip install -r requirements.txt
```

Main dependencies: Python, pandas, NumPy, scikit-learn, scikit-survival, XGBoost, CatBoost, PyTorch, pycox, matplotlib. Check `requirements.txt` for exact versions.

Place your SEER export in `data/` (this folder is git-ignored) and run the steps above in order.

## Limitations

- Retrospective registry data, with no systemic therapy details or molecular markers beyond ER/PR/HER2.
- Treatment assignment is not randomized, so treatment effects cannot be interpreted causally.
- No external validation on non-SEER cohorts yet.

Future work includes counterfactual survival modeling, causal inference, multi-modal data (pathology images, genomics), and external validation.


<!-- TODO: choose a license, e.g. MIT for code -->
