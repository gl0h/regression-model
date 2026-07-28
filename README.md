# Sony Regression Model
# Sony Quarterly Revenue Forecast

A time-series regression project forecasting Sony Group Corporation's (SONY) quarterly consolidated net revenue, built in Google Colab with `pandas`, `statsmodels`, and `matplotlib`.

## Overview

This notebook models and forecasts Sony's quarterly net revenue using 32 quarters of historical data (fiscal Q4-2017 through fiscal Q2-2026, i.e. Dec-2017 through Sep-2025). It compares a simple linear time-trend model against a model enriched with two dummy variables that capture known business dynamics, then uses the better model to forecast the next four quarters with 80% confidence intervals.

## Data

- **Source:** Macrotrends-compiled company financial statements
- **Ticker:** SONY
- **Metric:** Quarterly consolidated net revenue (USD millions)
- **Period:** 32 quarters, 2017-12-31 through 2025-09-30
- **Note:** Sony's fiscal year runs April–March, so fiscal Q3 (Oct–Dec) is the calendar holiday quarter

## Method

1. **Load & visualize** the quarterly revenue series to identify patterns.
2. **Engineer a `time` variable** (1, 2, 3, ...) representing sequential quarters, the basis for the trend model.
3. **Baseline model:** simple OLS regression of revenue on `time` alone.
4. **Feature engineering — two dummy variables identified from the plotted data:**
   - `holiday_dv`: 1 if the quarter is Oct–Dec (Sony's holiday fiscal Q3), else 0 — plus a `holiday_interaction` term (`time × holiday_dv`) to let the holiday effect grow/shrink over time
   - `ps5_dv`: 1 for quarters from the PS5 launch (Oct 2020) onward, else 0 — plus a `ps5_interaction` term (`time × ps5_dv`) to let that effect evolve over time
5. **Train/test split:** 75% training / 25% testing, in time order (no shuffling, since this is time-series data).
6. **Model comparison via MAPE** (Mean Absolute Percentage Error) on the held-out test quarters.
7. **Final forecast:** retrain the winning model on the full dataset and project revenue for the next 4 quarters with 80% prediction intervals.

## Results

| Model | Test MAPE |
|---|---|
| Baseline (time only) | 16.27% |
| Time + holiday + PS5 dummies (with interactions) | **8.59%** |

The dummy-variable model nearly halves the forecast error, confirming that the holiday-quarter spike and PS5-era revenue step-up are real, meaningful signals.

### Forecast — next 4 quarters (80% CI, USD millions)

| Quarter | Forecast | 80% CI |
|---|---|---|
| Dec-2025 (holiday) | 27,251 | 25,374 – 29,129 |
| Mar-2026 | 19,738 | 18,605 – 20,872 |
| Jun-2026 | 19,695 | 18,485 – 20,905 |
| Sep-2026 | 19,652 | 18,364 – 20,939 |

The forecast correctly reproduces the holiday-quarter spike followed by the sharp post-holiday drop seen throughout the historical data.

## Limitations

- Does not account for other product launches, competitive dynamics, or macroeconomic conditions beyond the PS5 effect
- Assumes the historical holiday and PS5 patterns continue linearly into the future
- Confidence intervals reflect model uncertainty only, not unforeseen shocks (e.g., supply chain disruptions, new console cycles)

## Requirements

```
numpy
pandas
matplotlib
statsmodels
```

## Files

- `Sony_Revenue_Forecast.ipynb` — main analysis notebook (Colab)
- `sony_qrevenue.csv` — input dataset (quarterly revenue, expected columns: `datadate`, `revenue_musd`, `calquarter`)

## How to Run

1. Open the notebook in Google Colab.
2. Upload `sony_qrevenue.csv` to the Colab environment (or mount Google Drive and update the file path).
3. Run all cells in order — the notebook loads data, visualizes it, fits the baseline and dummy-variable models, evaluates MAPE, and produces the 4-quarter forecast plot.
