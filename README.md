# Used Car Price Prediction — Data Analytics Final Project

**Author:** Raymond Widjaja (NIM: 2802474406) — Group 5

## Overview

This project analyzes a used-car sales dataset (~558,837 records) to clean the data, explore relationships between vehicle attributes and selling price, and build a machine learning model that predicts `sellingprice`. The workflow covers data quality assessment, data wrangling, exploratory data analysis (EDA), feature importance analysis, and model training/tuning with a Random Forest Regressor.

## Repository Contents

| File | Description |
|---|---|
| `Code.ipynb` | Jupyter notebook containing the full analysis pipeline: data cleaning, EDA, feature selection, model training, and hyperparameter tuning |
| `dataset.xlsx` | Raw car sales dataset used as input for the analysis |
| `Dokumentasi.pdf` | Written report covering data quality evaluation, analysis results, and interpretation of findings |

## Data Quality Issues Identified

- **Missing values:** ~19.24% (107,545 rows) of the dataset had missing values across columns such as `make`, `model`, `trim`, `body`, `transmission`, `condition`, `color`, `interior`, `vin`, `odometer`, `mmr`, `sellingprice`, and `saledate`.
- **Categorical inconsistencies:** Brand names such as "Mercedes-Benz" appeared in multiple inconsistent forms (e.g., `mercedes`, `mercedes-benz`, `mercedes-b`).
- **Outliers:** Numerical columns `sellingprice`, `mmr`, and `odometer` contained extreme outliers that could bias the model.

## Data Wrangling Steps

1. Dropped rows with missing values in critical categorical/identifier columns (`make`, `model`, `trim`, `body`, `transmission`, `vin`, `condition`, `sellingprice`, `saledate`).
2. Imputed missing numerical values (`odometer`, `mmr`) with the column median.
3. Filled missing categorical values (`color`, `interior`) with `"N/A"` instead of dropping.
4. Standardized categorical text using `str.lower()` and `str.strip()`, plus manual correction mapping for inconsistent brand/model/body names.
5. Removed outliers in `sellingprice`, `mmr`, and `odometer` using the IQR method.
6. Result: cleaned dataset retained 446,913 rows (~79.98% of the original data), with 0 missing values and 0 duplicates remaining.

## Exploratory Data Analysis

- **Odometer vs. Selling Price:** Inverted curvilinear trend — higher mileage correlates with lower selling price.
- **Year vs. Selling Price:** Positive trend — newer vehicles sell for more, reflecting steady depreciation.
- **Make vs. Selling Price (Top 10 brands):** BMW shows a higher, more stable median price; Chevrolet and Dodge show wider price ranges and more outliers. Several top brands (BMW, Chrysler, Ford) have lower whiskers touching $0, indicating some vehicles sold below MMR.

## Feature Importance (Mutual Information Score)

| Rank | Feature | MI Score |
|---|---|---|
| 1 | mmr | 1.660231 |
| 2 | model | 0.515187 |
| 3 | car_age | 0.410801 |
| 4 | trim | 0.402646 |
| 5 | odometer | 0.386676 |
| 6 | condition | 0.247342 |
| 7 | make | 0.171087 |
| 8 | state | 0.094388 |
| 9 | body | 0.077163 |
| 10 | interior | 0.038538 |
| 11 | color | 0.029011 |
| 12 | transmission | 0.002108 |

`mmr` (Manheim Market Report) is by far the strongest predictor, as it already serves as the market-benchmark price that the model can build on.

## Modeling

- **Algorithm:** Random Forest Regressor — chosen for its resistance to overfitting and ability to capture non-linear relationships.
- **Tuning approaches compared:**
  - Manual tuning (parameters set based on intuition/prior knowledge)
  - GridSearchCV (exhaustive search over a defined parameter grid, `cv=3`)

### Model Performance (Test Set)

| Configuration | MAE | RMSE | R² |
|---|---|---|---|
| Baseline (default params) | 885.32 | 1339.62 | 0.9672 |
| Manual tuning | 894.92 | 1347.61 | 0.9668 |
| GridSearchCV | 875.99 | 1327.35 | 0.9678 |

GridSearchCV produced the best-performing model, though the improvement over the baseline was marginal (~0.06% R² gain), suggesting the default Random Forest parameters were already close to optimal for this dataset.

## Key Insights & Recommendations

- Use `mmr`, `model`, `trim`, `odometer`, and `condition` as the core baseline for objective price prediction.
- Flag vehicles whose selling price falls anomalously below `mmr` (via a `price_diff` feature) to help inventory managers prioritize review instead of manual checking.
- These outputs can help dealers and auction houses set data-driven price baselines instead of relying solely on intuition or experience.

## Limitations

- The model is trained on a specific time period and may not generalize well to market shifts (e.g., fuel price changes, EV adoption, economic downturns).
- Heavy reliance on `mmr` means any bias in that benchmark will propagate into the model's predictions.
- Extreme high-mileage/high-value vehicles were removed during outlier handling (IQR method), so the model may underperform on such edge cases.

## Tech Stack

- Python (pandas, numpy, matplotlib, seaborn, scipy, scikit-learn)
- Jupyter Notebook

## How to Run

1. Clone this repository.
2. Install dependencies: `pip install pandas numpy matplotlib seaborn scipy scikit-learn`
3. Ensure the dataset (`car_prices.csv` / `dataset.xlsx`) is in the same directory as `Code.ipynb`.
4. Open and run `Code.ipynb` in Jupyter Notebook or JupyterLab.

## Team & Contributions
This project was collaboratively developed by:
*   **Ofa Sofwatan Munifa:** Report Creation and Data Processing
*   **Herrick Fabian:** Report Creation and Data Processing.
*   **Raymond Widjaja:** Report Creation and Data Processing.
