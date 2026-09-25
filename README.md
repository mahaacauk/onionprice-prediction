# Onion Price Prediction using Machine Learning

Predicts onion mandi prices using regression models, with seasonal pattern and weather-based features, plus a prompt-driven query assistant. Built as a machine learning assignment.

**Live demo:** open `index.html` in this repo, or enable GitHub Pages (see below) to get a public link — anyone can open it, type a question like "price in Kerala during monsoon," and get an instant answer. No install, no server, works in any browser.

## Problem Statement

Commodity price fluctuations affect both farmers and consumers. This project predicts future onion prices based on historical market trends, seasonal patterns, and weather-related factors.

## Repository Contents

| File | Description |
|---|---|
| `index.html` | Standalone dashboard + price-query chat assistant. Open directly in a browser. |
| `ML_ASSIGNMENT_2_DOCUMENTED.ipynb` | Full Jupyter notebook: EDA, feature engineering, model training/tuning, dashboard, chat. |
| `onion_price_dataset_with_weather.csv` | Dataset used (see note on simulated fields below). |
| `README.md` | This file. |

## Dataset Note (Important)

The original source data (Agmarknet-style mandi prices) contained only a **single date** and **no weather columns**. To meet the assignment's requirement of seasonal and weather-based prediction, the dataset was extended:
- **Real:** State, District, Market, Variety, Grade, and baseline prices.
- **Simulated:** weekly dates across one year (Oct 2025–Sep 2026), and weather columns (`Rainfall_mm`, `Temperature_C`, `Humidity_percent`), generated using realistic Indian seasonal patterns (e.g. monsoon rainfall peaks Jun–Sep).

This is disclosed for transparency; a real deployment would use actual historical price and weather records.

## Methodology

1. **EDA** — distribution, correlation, and market/district comparisons of prices.
2. **Feature engineering** — `Year`/`Month`/`Day` extracted from date; `Season` (Winter/Summer/Monsoon/Post-Monsoon) derived from month as a proxy for weather-driven effects.
3. **Model training** — Simple Linear Regression, Multiple Linear Regression, Polynomial Regression, Ridge Regression, Random Forest.
4. **Hyperparameter tuning** — Random Forest evaluated across `n_estimators` ∈ {10, 50, 100, 200} × `max_depth` ∈ {5, 10, None}, compared via MAE, RMSE, R².
5. **Prompt-based query / RAG chat** — natural-language questions ("price in Punjab during Monsoon?") are answered by retrieving the matching subset of the data and generating a response from it (retrieval-augmented generation pattern).
6. **Dashboard** — model comparison, seasonal price averages, price trend over time, feature importances.

## Prompts Used (Data Query / RAG)

Documented exactly as tested:
- "What is the average onion price in Punjab during Monsoon?"
- "What is the onion price in Tamil Nadu in Winter?"
- "What is the average price during Summer?"
- "What is the price in Rajasthan?"

## Model Performance (Hyperparameter Comparison)

Random Forest, full feature set (location + variety + grade + date + season + weather):

| n_estimators | max_depth | MAE | RMSE | R² |
|---|---|---|---|---|
| 10 | 5 | 552.67 | 710.01 | 0.8089 |
| 10 | 10 | 443.65 | 570.96 | 0.8764 |
| 10 | None | 259.53 | 345.87 | 0.9547 |
| 50 | 5 | 556.34 | 713.90 | 0.8068 |
| 50 | 10 | 439.34 | 565.68 | 0.8787 |
| 50 | None | 248.17 | 329.93 | 0.9587 |
| 100 | 5 | 557.63 | 714.66 | 0.8064 |
| 100 | 10 | 439.37 | 565.71 | 0.8787 |
| 100 | None | 248.00 | 329.21 | 0.9589 |
| **200** | **None** | **247.88** | **328.90** | **0.9590** |
| 200 | 5 | 557.99 | 715.38 | 0.8060 |
| 200 | 10 | 439.10 | 565.71 | 0.8787 |

**Best:** `n_estimators=200, max_depth=None` — tree depth matters far more than tree count for this data.

## How to Run the Notebook

```
pip install pandas numpy scikit-learn matplotlib seaborn ipywidgets
jupyter notebook ML_ASSIGNMENT_2_DOCUMENTED.ipynb
```
Run all cells top to bottom (Kernel → Restart Kernel and Run All Cells).
