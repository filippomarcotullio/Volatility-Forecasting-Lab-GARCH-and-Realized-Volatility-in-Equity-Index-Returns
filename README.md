# Volatility Forecasting Lab – GARCH(1,1) on S&P 500

This project investigates the dynamics of equity index volatility using daily S&P 500 returns (2010–2024).  
It combines realized volatility measurement, GARCH(1,1) modelling, and a simple volatility-targeting strategy to assess whether conditional volatility forecasts can improve portfolio risk management.

---

## Overview

Financial markets exhibit volatility clustering: periods of calm and turbulence tend to persist.  
Modelling this behaviour is central to risk management, derivative pricing, and portfolio construction.

This project:

- computes realized volatility using rolling windows;
- estimates a GARCH(1,1) model on daily log-returns;
- compares conditional volatility forecasts with realized volatility;
- applies the forecast to a volatility-targeting trading rule;
- evaluates performance against a passive Buy & Hold benchmark.

The goal is to assess the practical usefulness of conditional volatility estimates.

---

## Dataset

- Asset: S&P 500 Index (`^GSPC`)
- Frequency: Daily
- Period: 2010–2024  
- Source: Yahoo Finance via `yfinance`

Returns are computed as log-returns.  
Volatility is annualised assuming 252 trading days.

---

## Methodology

### 1. Realized Volatility

Realized volatility is estimated using a 21-day rolling standard deviation of log-returns:

\[
\text{RV}_{t} = \sqrt{252} \cdot \text{std}(r_{t-20}, \dots, r_{t})
\]

This smooths high-frequency noise while remaining reactive to market shocks.

### 2. GARCH(1,1) Model

The conditional variance is modelled using the standard GARCH(1,1) specification:

\[
\sigma_t^2 = \omega + \alpha \varepsilon_{t-1}^2 + \beta \sigma_{t-1}^2
\]

Daily innovations follow:

\[
r_t = \mu + \varepsilon_t,\qquad \varepsilon_t = \sigma_t z_t,\quad z_t \sim N(0,1)
\]

Parameters are estimated via Maximum Likelihood using the `arch` package.

### 3. Volatility-Targeting Strategy

The conditional volatility forecast is used to scale portfolio exposure:

\[
w_t = \frac{\sigma_{\text{target}}}{\hat{\sigma}_{t}}
\]

where the target volatility is set to 10% annualised.  
Weights are bounded between 0 and 2 to avoid excessive leverage or short exposure.

Two equity curves are constructed:

- Buy & Hold: fully invested at all times  
- Volatility-Targeted Portfolio: exposure adjusted based on the GARCH forecast  

---

## Results

### Volatility Comparison  
Realized volatility vs. GARCH(1,1) conditional volatility.

![Volatility Comparison](plots/volatility_comparison.png)

The GARCH model captures persistent volatility regimes but exhibits the typical lag during sudden market shocks.

### Equity Curve: Buy & Hold vs. Volatility Targeting

![Equity Curve](plots/equity_curves.png)

Volatility targeting results in a smoother equity curve, reduced drawdowns, and competitive risk-adjusted performance relative to passive exposure.

---

## Performance Metrics

The following metrics are computed:

- Annualised Sharpe ratio  
- Maximum drawdown  
- MSE and MAE between realized and GARCH volatility  
- Annualised volatility of returns  

These measures allow a direct comparison between the predictive performance of the GARCH model and its practical usefulness for portfolio risk management.

---

## How to Run

```bash
pip install -r requirements.txt
python volatility_forecasting_garch.py
