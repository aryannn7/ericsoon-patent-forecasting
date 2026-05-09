# Ericsoon-Patent-Forecasting
Ericsson patent activity forecasting using Linear Regression, Random Forest, and XGBoost. Includes EDA, baseline forecasting, and segmentation-based cluster forecasting. Dataset: 30,118 patents from 1976 to 2025


# Ericsson Patent Activity Forecasting

A machine learning project analysing and forecasting Ericsson's patent filing 
behaviour across five decades. Built as coursework for BMAN60422 (Data Strategy 
and Visualisation) at The University of Manchester.

---

## What this project does

Ericsson files thousands of patents every year. Patents are their competitive 
weapon — they license them to companies like Apple and Samsung, generating 
billions in annual revenue. This project asks: can we predict how many patents 
Ericsson will file next year, and does understanding *what kind* of patents 
they file help us forecast better?

To answer that, I built a full machine learning pipeline covering:

- Exploratory analysis of 30,118 patents filed between 1976 and 2025
- Three forecasting models compared head-to-head: Linear Regression, Random Forest, and XGBoost
- Two clustering schemes that segment patents by topic and technology area
- Cluster-level forecasting that tests whether segmentation improves accuracy

---

## Results at a glance

| Model | Test MAPE | What it means |
|---|---|---|
| Linear Regression | 15.79% | Off by ~190 patents/year on average |
| XGBoost | 13.77% | Better on validation, weaker on unseen data |
| **Random Forest** | **10.87%** | **Best generalisation — chosen model** |

Random Forest won not because it looked best during development (XGBoost did) 
but because it generalised most reliably to genuinely unseen test years. 
That distinction matters in real forecasting.

**Key finding:** The single strongest predictor of this year's patent count is 
last year's patent count. Ericsson's patent activity is driven by operational 
momentum — R&D pipelines, legal budgets, inventor headcount — not by which 
technology area is currently growing.

---

## Project structure
ericsson-patent-forecasting/
│
├── Ericsson_Patent_Forecasting_Complete.ipynb   ← main notebook (run this)
├── dataset/
│   └── ericsson_patent_rich_dataset.csv         ← 30,118 patents, 28 variables
├── linear_regression_model.pkl                  ← saved LR model
├── random_forest_model.pkl                      ← saved RF model
└── README.md

---

## How to run it

**Option 1 — Google Colab (recommended, no setup needed)**

1. Open the notebook in Colab
2. Upload `ericsson_patent_rich_dataset.csv` to your Google Drive
3. Mount your Drive in the first cell and update `DATA_PATH`
4. Run cells from top to bottom

**Option 2 — Local machine**

```bash
git clone https://github.com/yourusername/ericsson-patent-forecasting
cd ericsson-patent-forecasting
pip install pandas numpy matplotlib seaborn scikit-learn xgboost joblib
jupyter notebook Ericsson_Patent_Forecasting_Complete.ipynb
```

Place the CSV file inside a folder called `dataset/` before running.

---

## What the notebook covers

### Task 1 — Exploratory Data Analysis
Understanding the data before touching any model. Annual patent trends, 
technology era evolution, keyword shifts over time, and portfolio composition. 
Every modelling decision in Tasks 2 and 3 is justified by something found here.

### Task 2 — Baseline Forecasting
Three models built and compared. The key engineering choices:

- **Features:** lag_1 (last year's count), lag_2, momentum, rolling 3-year 
  average — all time-series features grounded in EDA findings
- **Split:** strictly chronological — train on 1979–2015, validate on 2016–2019, 
  test on 2020–2025. Random splitting is never used because it leaks future 
  information into training
- **Evaluation:** MAE, RMSE, MAPE, and R². MAPE is the headline metric because 
  it is readable in percentage terms and comparable across different time periods

### Task 3 — Segmentation and Cluster Forecasting
Two ways of grouping patents into clusters:

**Scheme 1 — Text-based:** TF-IDF converts patent titles into numbers. SVD 
compresses 8,000 word dimensions down to 50. K-Means groups patents by what 
they say. The number of clusters is chosen using the elbow method and silhouette 
score together.

**Scheme 2 — Feature-based:** Clusters patents using domain knowledge variables 
— technology keyword flags, utility type indicator, keyword richness score, and 
technology era. StandardScaler ensures all features contribute equally.

Each cluster gets its own independent LR and RF models. Predictions are summed 
across clusters and compared against the non-segmented baselines.

---

## AI engineering concepts demonstrated

This project intentionally applies production ML engineering principles to a 
classical machine learning problem. The final section of the notebook maps each 
concept explicitly.

| Concept | How it appears in this project |
|---|---|
| Embeddings | TF-IDF + SVD converts patent titles to 50-dimensional vectors |
| Evaluation rigour | Three-window chronological evaluation, MAPE as primary metric |
| Preventing data leakage | Chronological split, shift-before-rolling to avoid lookahead |
| Model selection trade-offs | RF chosen over XGBoost based on test generalisation, not validation peak |
| Map-reduce architecture | Cluster-level forecasting: map (per-cluster models) → reduce (aggregate) |
| Constrained outputs | Regression output is a number verifiable against ground truth — no hallucination |

---

## Dataset

The dataset contains Ericsson patents from 1976 to 2025 with 28 variables 
covering temporal identifiers, patent type flags, nine technology keyword 
binary flags (5G, AI/ML, cloud, security, IoT, network, energy, antenna, data), 
title text statistics, and keyword richness scores.

Not included in this repository due to file size. Add your copy to `dataset/` 
before running.

---

## Tech stack

Python 3.10 · pandas · numpy · scikit-learn · XGBoost · matplotlib · seaborn · joblib

---

## About

Built by Aryan as part of a project at The University of Manchester. 

If you found this useful or want to discuss the methodology, feel free to 
open an issue or connect.
