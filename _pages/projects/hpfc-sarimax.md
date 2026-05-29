---
layout: single
title: "Hourly Price Forward Curve - SARIMAX Forecasting"
permalink: /projects/hpfc-sarimax/
toc: true
toc_label: "Contents"
toc_icon: "chart-line"
---

<div class="project-meta">
  <span class="tag">Python</span>
  <span class="tag">SARIMAX</span>
  <span class="tag">statsmodels</span>
  <span class="tag">Time Series</span>
  <span class="tag">Energy Markets</span>
</div>

---

## Overview

Forward contracts are one of the most critical instruments in power trading, as locking in future electricity prices provides revenue certainty and hedges against spot market volatility. An **Hourly Price Forward Curve (HPFC)** models these future prices at hourly granularity, often projected years ahead.

This project develops a full pipeline to construct an HPFC for the **German day-ahead electricity market (EPEX SPOT)**: from raw data exploration and stationarity testing, through SARIMAX model design and hyperparameter optimisation, to multi-strategy backtesting and a final 2024 hourly price forecast, which is scaled to an assumed Cal-27 forward market price.

---

## The Data

**Dataset:** 5 years of hourly German day-ahead electricity prices (2019–2023), totalling ~42,800 observations.

| Statistic | Value |
|---|---|
| Mean price | 99.08 €/MWh |
| Std. deviation | 105.56 €/MWh |
| Global minimum | −500 €/MWh |
| Global maximum | +871 €/MWh |

The first step was to identify and quantify recurring patterns at four time scales: **daily, weekly, seasonal,** and **crisis-driven**.

<figure>
  <img src="/assets/images/projects/hpfc-sarimax/hpfc_daily_pattern.png" alt="Daily price patterns showing the duck curve effect">
  <figcaption>Daily patterns in German electricity prices. The characteristic "Duck Curve" shows twin peaks during morning and evening demand, a solar-driven noon dip, and occasional negative prices during solar surplus. Peak hour: 19:00 (133 €/MWh); Off-peak: 03:00 (77 €/MWh).</figcaption>
</figure>

<figure>
  <img src="/assets/images/projects/hpfc-sarimax/hpfc_seasonal_pattern.png" alt="Seasonal price patterns across months and seasons">
  <figcaption>Seasonal price structure. August is the most expensive month (avg. 143 €/MWh, σ = 178 €/MWh) driven by cooling demand and renewable volatility. May is the cheapest (avg. 74 €/MWh), when high wind generation keeps prices suppressed.</figcaption>
</figure>

<figure>
  <img src="/assets/images/projects/hpfc-sarimax/hpfc_crisis_pattern.png" alt="Russia-Ukraine energy crisis impact on electricity prices in 2022">
  <figcaption>The Russia–Ukraine conflict (Feb 2022) caused TTF gas prices to surge, pushing German electricity prices to nearly 4× their 2021 levels by August 2022. This crisis signature is explicitly encoded as an exogenous variable in the model.</figcaption>
</figure>

---

## Model Architecture

The model of choice is **SARIMAX**: Seasonal ARIMA with eXogenous regressors. The three-tier progression makes the reasoning clear:

| Model | Core Capability | Limitation addressed |
|---|---|---|
| **ARIMA** | Trend + autocorrelation, handles non-stationarity | No seasonality, no external factors |
| **SARIMA** | Adds explicit seasonal cycles | No external regressors |
| **SARIMAX** | All of SARIMA + exogenous variables | - |

SARIMAX is the right tool here because electricity prices are simultaneously **seasonal** (driven by climate and human activity cycles) and **externally influenced** (gas prices, weekends, crises).

### Stationarity Check

ARIMA-family models require a stationary time series, one whose mean and variance do not drift over time. The series was tested using the **Augmented Dickey-Fuller (ADF) test**. Where non-stationarity was detected, differencing (the *I* term in ARIMA) was applied to stabilise it before fitting.

### Hyperparameter Optimisation

SARIMAX is governed by 7 hyperparameters: `(p, d, q)` for the ARIMA part and `(P, D, Q, s)` for the seasonal component. A **Grid Search** was run across all feasible combinations, selecting the configuration that minimised the **Akaike Information Criterion (AIC)**, a principled measure of model fit penalised for complexity.

**Best-fit parameters (final forecast model):**

order = (p=1, d=0, q=3)
seasonal = (P=2, D=1, Q=5, s=24)


The seasonal period `s=24` explicitly models the **daily cycle** in hourly data. Expanding to `s=168` (weekly) would capture richer seasonality but would exceed memory limits on a standard machine; this is a direction for future work on a compute cluster.

### Exogenous Variables

The following external regressors were included to capture market behaviour that pure seasonality cannot explain:

- **Peak / off-peak hour flag**: demand surges during 08:00–20:00
- **Weekday / weekend flag**: commercial and industrial load pattern
- **TTF natural gas price**: sets the marginal cost of electricity via gas-fired plants in the merit order
- **Sinusoidal hour-of-day variable**: smooth, continuous encoding of the intra-day price cycle
- **Crisis flag (Feb–Dec 2022)**: isolates the anomalous price regime of the Russia–Ukraine energy shock

---

## Backtesting

Three backtesting strategies were implemented and compared. All strategies respect temporal order; the model is never trained on data from the future.

**Static Train-Test Split** serves as the performance baseline: a single 80/20 split of the full dataset. Fast and interpretable. Results: **MAE = 7.51 €/MWh, RMSE = 9.12 €/MWh**.

**Expanding Window** progressively grows the training set from a 60-day seed, forecasting a 7-day horizon at each step. The model improves over iterations as it accumulates more historical context, but is computationally expensive due to re-fitting on an ever-growing dataset.

**Sliding Window** is the primary backtesting strategy. The training set is fixed at **90 days**, the test set at **14 days**, and the entire window slides forward at each iteration. This makes it significantly faster than the expanding approach while remaining robust to regime changes; older data is discarded over time. The trade-off is reduced sensitivity to long-term seasonality; in practice, RMSE remains stable for the majority of the test period, with spikes concentrated around the 2022 crisis window.

<figure>
  <img src="/assets/images/projects/hpfc-sarimax/hpfc_backtest_static.png" alt="Static train-test split backtesting results">
  <figcaption>Static train-test split backtesting (training: 6 months, test: 1 month). The bottom panel zooms into the test period, showing the model closely tracking actual price dynamics. MAE: 7.51 €/MWh, RMSE: 9.12 €/MWh.</figcaption>
</figure>

For full backtesting plots across all three strategies, including expanding window RMSE evolution and sliding window rolling MAE, see the [GitHub repository](https://github.com/devanshu-cosmo/ml-projects/tree/main/Time_Series_Data){:target="_blank"}.

---

## 2024 Forecast

The final HPFC forecast covers the full 2024 calendar year at hourly resolution. Four model configurations were compared, trained on 1, 2, 3, and 5 years of history, respectively. The hourly shapes are nearly identical across all four, confirming that **the 3 most recent years are sufficient** to capture the HPFC structure.

The figure below compares two configurations:

- **Model A**: trained on 2023 only (last 1 year)
- **Model B**: trained on 2022–2023 (last 2 years, includes the energy crisis)

<figure>
  <img src="/assets/images/projects/hpfc-sarimax/hpfc_forecast_2024.png" alt="2024 hourly price forecast comparison between Model A and Model B">
  <figcaption>2024 HPFC forecast. Both models are scaled so that the yearly average equals 85 €/MWh (assumed Cal-27 forward price). Model B, trained on the 2022 crisis period, exhibits higher amplitude oscillations reflecting heightened sensitivity to market volatility. The overall shape is stable across both setups.</figcaption>
</figure>

---

## Pipeline Summary

→ Data Cleaning & EDA
→ Stationarity Check (ADF Test)
→ Exogenous Variable Engineering
→ Grid Search (AIC-optimised hyperparameters)
→ SARIMAX Model Fit
→ Backtesting (Static / Expanding / Sliding Window)
→ 2024 HPFC Forecast
→ Cal-27 Forward Price Scaling

---

<div class="project-links-bar">
  <a href="https://github.com/devanshu-cosmo/ml-projects/tree/main/Time_Series_Data" target="_blank" class="btn btn--primary">
    <i class="fab fa-github"></i> View on GitHub
  </a>
  <a href="/assets/files/Devanshu_SARIMAX_Report_compressed.pdf" target="_blank" class="btn btn--inverse">
    <i class="fas fa-file-pdf"></i> Download Full Report
  </a>
</div>
