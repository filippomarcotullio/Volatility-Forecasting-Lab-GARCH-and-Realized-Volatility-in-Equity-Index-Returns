# Volatility Forecasting Lab – GARCH(1,1) on S&P 500

This project studies equity index volatility using daily S&P 500 returns (2010–2024).  
It combines realized volatility, GARCH modelling, and a volatility-targeting rule to evaluate whether volatility forecasts improve practical risk management.

---

## Overview

Equity market volatility tends to cluster: calm periods are followed by calm periods, and turbulent periods by turbulence.  
Understanding and forecasting this behaviour is essential in risk management, derivatives pricing, and portfolio construction.

This project:

- computes realized volatility using rolling windows,
- estimates a GARCH(1,1) model on daily log-returns,
- compares conditional volatility estimates with realized volatility,
- applies the forecast to a volatility-targeting allocation rule,
- evaluates performance against a Buy & Hold benchmark.

---

## Dataset

- Asset: S&P 500 Index (^GSPC)  
- Frequency: Daily  
- Period: 2010–2024  
- Source: Yahoo Finance via `yfinance`

Returns are computed as log-returns.  
Volatility is annualised using 252 trading days.

---

## Methodology

### 1. Realized Volatility

Realized volatility is computed as the standard deviation of the past 21 trading days of log-returns, annualised.  
This provides a smooth, data-driven estimate of volatility observed in the market.

In words:  
**Realized Volatility = (21-day rolling standard deviation of returns) × sqrt(252)**

---

### 2. GARCH(1,1) Model

A GARCH model describes the evolution of volatility over time.  
The GARCH(1,1) specification states that:

- tomorrow’s volatility depends on:
  - a long-run average level (omega),
  - yesterday’s squared return shock (alpha),
  - yesterday’s volatility estimate (beta).

In words:  
**Variance tomorrow = omega + alpha × (yesterday’s shock)^2 + beta × (yesterday’s variance)**

The return is written as:  
**return = mean + volatility × random shock**

Parameters are estimated using maximum likelihood through the `arch` library.

---

### 3. Volatility-Targeting Strategy

The model’s volatility forecast is used to adjust portfolio exposure.

Daily portfolio weight is computed as:

**weight = target volatility / forecast volatility**

where target volatility is set to 10% annualised.  
Weights are constrained between 0 and 2 (no shorting, max 2× leverage).

Two equity curves are built:

- Buy & Hold: exposure = 1  
- Volatility-Targeted: exposure varies over time  

This allows evaluating whether volatility forecasts provide a useful signal for risk control.

---

## Results

### Realized Volatility vs. GARCH Volatility

![Volatility Comparison](./volatility_comparison.png)

The GARCH model captures broad volatility regimes but reacts with delay during sharp volatility spikes.

---

### Buy & Hold vs. Volatility-Targeting Portfolio

![Equity Curve](./equity_curves.png)

The volatility-targeting portfolio shows lower drawdowns and smoother performance while maintaining competitive returns.

---

## Performance Metrics

The project computes:

- annualised Sharpe ratio,  
- maximum drawdown,  
- annualised return volatility,  
- MSE and MAE between realized and GARCH volatilities.

These metrics provide a quantitative comparison of predictive accuracy and portfolio performance.

---

## How to Run
