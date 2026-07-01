# US Consumer Inflation Expectations vs. Actual Inflation

A time-series analysis and rolling-window regression study testing the **rationality of consumer inflation expectations** in the United States — comparing survey-based *predicted* inflation against *actual* CPI-based inflation over multiple decades of data.

---

## Project Overview

This project investigates a classic question in behavioral economics: **do consumers form rational expectations about future inflation, or do their forecasts systematically deviate from actual inflation?**

It combines:
- **Survey-based inflation expectations** (`PX1`, from a consumer survey dataset — `price_month.csv`)
- **Actual U.S. inflation** (`FPCPITOTLZGUSA` — annual CPI-based inflation, likely sourced from World Bank/FRED-style data in `monthly.csv`)

The analysis progresses from data cleaning and exploratory visualization through to a **240-month rolling-window OLS regression**, testing how the relationship between predicted and actual inflation evolves over time and across different economic periods.

---

## Pipeline / Notebook Structure

The notebook (`m.ipynb`) is organized into the following stages:

### 1. Data Cleaning
- Loads raw survey data (`price_month.csv`)
- Removes invalid survey response codes (`-97, 96, 98, 99`) from the `PX1` column
- Drops missing values
- Saves cleaned data to `cleaned_dataset.csv`

### 2. Aggregation
- Splits `YYYYMM` into `year` and `month`
- Computes **monthly mean** inflation expectations → `monthly_means.csv`
- Computes **yearly mean** inflation expectations → `yearly_means.csv`

### 3. Exploratory Data Analysis (EDA)
- Time series plots of yearly and monthly mean inflation expectations
- Distribution plots (Gaussian/KDE) of monthly and yearly means using `seaborn`
- Boxplots of inflation by month and by year (1978–2023) to visualize seasonal and long-term variation
- Comparison plots of **predicted vs. actual inflation** over time

### 4. Merging Predicted & Actual Inflation
- Combines survey-based predicted inflation (`PX1`) with actual CPI inflation (`FPCPITOTLZGUSA`) into a single aligned dataset
- Renames columns for clarity: `PX1` → `PredictedInflation`, `FPCPITOTLZGUSA` → `ActualInflation`

### 5. Static Linear Regression
- Runs an initial OLS regression (`statsmodels`) between predicted and actual inflation (both directions tested) to establish a baseline relationship
- Includes **rationality diagnostics**:
  - **Shapiro-Wilk test** — normality of residuals
  - **Breusch-Pagan test** — homoscedasticity (constant variance) of residuals

### 6. Rolling-Window Regression (Core Analysis)
- Builds a rolling dataset (`combined.csv`) with:
  - `y` — target inflation series
  - `x1` — actual inflation (`FPCPITOTLZGUSA`)
  - `x2` — lagged/alternate predicted inflation series
- Runs **OLS regression on a 240-month rolling window**, sliding one month at a time across the full dataset
- For each window, records:
  - Regression coefficients (`const`, `x1`, `x2`)
  - **R²** and **Adjusted R²**
  - **RMSE**
  - Standard errors and peak (min/max) values for each coefficient
- Visualizes how coefficients, R², and error metrics evolve across rolling windows over time — highlighting periods where the predicted/actual inflation relationship strengthens or breaks down (e.g. around major economic shocks)

---

## Key Techniques Used

| Technique | Purpose |
|---|---|
| OLS Regression (`statsmodels`) | Model the relationship between predicted and actual inflation |
| Rolling-window regression (240-month window) | Track how this relationship changes over time |
| Shapiro-Wilk test | Test residual normality (rationality diagnostic) |
| Breusch-Pagan test | Test residual homoscedasticity (efficiency diagnostic) |
| Seaborn KDE / boxplots | Visualize distribution and seasonality of inflation expectations |

---

## Getting Started

### Prerequisites
```bash
pip install pandas numpy matplotlib seaborn statsmodels scikit-learn
```

### Required Input Files
Place the following files in the same directory as the notebook before running:
- `price_month.csv` — raw survey data containing `PX1` and `YYYYMM` columns
- `monthly.csv` — actual inflation data containing `FPCPITOTLZGUSA`

### Running
Open `m.ipynb` in Jupyter Notebook / JupyterLab and run all cells sequentially. The notebook will generate several intermediate CSV files along the way (`cleaned_dataset.csv`, `monthly_means.csv`, `yearly_means.csv`, `combined.csv`, etc.), which later cells depend on — **run top to bottom** rather than skipping around.

---

