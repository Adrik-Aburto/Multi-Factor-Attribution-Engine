# Multi-Factor Equity Return Attribution Engine

## Project Overview

This project is an automated quantitative research pipeline that decomposes the historical returns of individual equities into systematic risk factors and estimated stock-specific performance ($\alpha$).

Instead of relying on the traditional single-factor Capital Asset Pricing Model (CAPM), this engine implements the **Fama-French 5-Factor Model** (Market, Size, Value, Profitability, and Investment). By utilizing static and rolling time-series regressions, the engine measures changing factor exposures ("style drift") and generates return attribution waterfalls, allowing systematic factor-driven performance to be separated from the model's estimated stock-specific component.

The analysis uses a fixed five-year sample period, with the final usable dataset spanning **July 2, 2021 through June 29, 2026** after aligning equity and factor data.

## Core Features

- **Automated Data Pipeline:** Fetches and aligns daily adjusted equity prices (via `yfinance`) with daily factor returns (via `pandas_datareader` / Kenneth French Data Library).
- **Robust Time-Series Alignment:** Handles timezone discrepancies, missing data, and trading holiday mismatches using normalized Datetime indices and inner joins.
- **Econometric Modeling:** Implements full-sample and 126-day rolling Ordinary Least Squares (OLS) regressions using `statsmodels`.
- **Look-Ahead Bias Prevention:** Shifts rolling factor exposures by one trading day before calculating return attribution, ensuring only information available prior to each return observation is used.
- **Return Attribution:** Calculates the daily contribution of each Fama-French factor, estimated alpha, and residual return to historical excess returns.
- **Comparative Analysis:** Compares AAPL and JPM to examine differences in growth/value characteristics and return drivers.
- **Visualization:** Generates return attribution waterfalls, rolling factor exposure plots, and comparative factor heatmaps using `matplotlib`, `seaborn`, and `plotly`.

## The Mathematical Framework

The engine calculates excess returns over the risk-free rate ($R_f$) and fits the following multivariate regression:

$$R_{it} - R_{ft} = \alpha_i + \beta_1(MKT_t) + \beta_2(SMB_t) + \beta_3(HML_t) + \beta_4(RMW_t) + \beta_5(CMA_t) + \epsilon_{it}$$

Where:

- **$\alpha$** = Estimated stock-specific component not explained by the modeled factors
- **$\beta_x$** = Exposure to the corresponding systematic risk factor
- **$\epsilon$** = Residual return not explained by the model

Rolling 126-day regressions are used to measure how factor exposures change over time. For return attribution, rolling factor exposures are shifted by one trading day to prevent look-ahead bias.

## Key Findings

Using the five-year analysis period, the model produced the following results:

- **AAPL:** Average 126-day HML exposure of **-0.496**, consistent with growth-oriented characteristics. Its average market beta was **1.183**, with the market factor contributing **+50.95 percentage points** to cumulative additive return attribution and estimated alpha contributing **+5.97 percentage points**.
- **JPM:** Average 126-day HML exposure of **+0.933**, consistent with value-oriented characteristics. Estimated alpha contributed **+27.47 percentage points** to cumulative additive return attribution.
- **Comparative Insight:** The results demonstrate how two large-cap equities can exhibit substantially different factor profiles and modeled return drivers, even when both have market betas above 1.

> **Note:** Factor contributions are additive daily return contributions summed over the sample, while cumulative excess returns are compounded. Therefore, attribution contributions should not be interpreted as percentages of the stock's total compounded return.

## Visual Output Examples

### 1. Factor Exposure Heatmap (AAPL vs. JPM)

*Compares full-sample Fama-French factor exposures between AAPL and JPM, highlighting the contrast between Apple's negative Value (HML) exposure and JPM's positive Value exposure.*

### 2. Rolling 126-Day Factor Exposures (Style Drift)

*Tracks how AAPL's estimated factor exposures change over time, illustrating that a stock's systematic risk profile can evolve rather than remain constant.*

### 3. Cumulative Return Attribution Waterfall

*Decomposes cumulative additive return contributions across the Fama-French factors, estimated alpha, and residual return.*

## Technology Stack

- **Languages:** Python
- **Data Manipulation:** `pandas`, `numpy`
- **Statistical Modeling:** `statsmodels`
- **Data Acquisition:** `yfinance`, `pandas_datareader`
- **Visualization & Export:** `plotly`, `matplotlib`, `seaborn`, `kaleido`

## How to Run

1. Clone this repository.
2. Install the required dependencies: `pip install yfinance pandas-datareader statsmodels plotly seaborn kaleido==0.2.1`
3. Open the `.ipynb` notebook in Jupyter or Google Colab and run all cells sequentially.

The notebook uses a fixed analysis window ending June 30, 2026 to ensure reproducibility rather than dynamically changing the sample based on the current date.
