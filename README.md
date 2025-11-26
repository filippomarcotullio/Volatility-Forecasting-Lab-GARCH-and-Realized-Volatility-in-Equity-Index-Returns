# Volatility Forecasting Lab – GARCH(1,1) on S&P 500

This project explores the dynamics of equity index volatility using daily S&P 500 returns (2010–2024).  
It combines **realized volatility**, **GARCH modelling**, and a **volatility-targeting strategy**, providing a compact yet rigorous example of quantitative risk modelling.

---

## Overview

Financial markets exhibit *volatility clustering*: periods of calm and turbulence tend to persist.  
This behaviour is central to risk management, derivatives pricing, and portfolio construction.

This project:

- computes **realized volatility** using rolling windows,
- fits a **GARCH(1,1)** model to daily log-returns,
- compares the conditional volatility forecast with realized volatility,
- applies the forecast to a **volatility-targeting trading rule**,  
- evaluates performance vs. passive Buy & Hold.

The goal is to understand whether estimated conditional volatility can improve risk-adjusted returns.

---

## Dataset

- **Asset:** S&P 500 Index (`^GSPC`)
- **Frequency:** Daily
- **Period:** 2010–2024  
- **Source:** Yahoo Finance via `yfinance`

Returns are computed as log-returns.  
Volatility is annualised assuming 252 trading days.

---

## Methodology

### **1. Realized Volatility**
Estimated using a **21-day rolling standard deviation** of log-returns:

- short enough to respond to shocks  
- long enough to smooth noise  
- annualised via √252

### **2. GARCH(1,1) Modelling**

A GARCH(1,1) specification:

\[
\sigma^2_t = \omega + \alpha \varepsilon^2_{t-1} + \beta \sigma^2_{t-1}
\]

captures volatility clustering by combining:

- persistence (β),
- reaction to shocks (α),
- long-term volatility level (ω).

Parameter estimation is performed via Maximum Likelihood using the `arch` library.

### **3. Volatility Targeting Strategy**

A simple, practical risk-management rule:

\[
\text{weight}_t = \frac{\sigma_{\text{target}}}{\hat{\sigma}_{t}}
\]

Where:

- σ̂ₜ is the GARCH forecast (shifted one day to avoid look-ahead bias),
- σ_target = 10% annual target volatility.

Weights are bounded between 0 and 2 (no shorting, max 2× leverage).

Two equity curves are computed:

- **Buy & Hold** (always fully invested)
- **Volatility-Targeted Portfolio**

---

## Results

### **Volatility Comparison**  
GARCH conditional volatility vs. 21-day realized volatility.

![Volatility Comparison](plots/volatility_comparison.png)

The GARCH model tracks broad volatility regimes, though—as expected—it lags during sudden spikes.

---

### **Equity Curve: Buy & Hold vs. Vol-Targeting**

![Equity Curve](plots/equity_curves.png)

The volatility-targeting rule delivers:

- smoother path,
- reduced drawdowns,
- comparable or improved risk-adjusted performance.

---

## Performance Metrics

Metrics computed:

- **Sharpe Ratio (annualised)**
- **Maximum Drawdown**
- **Volatility of returns**
- **MSE/MAE between realized and GARCH volatility**

These allow objective comparison between volatility forecasting and the realised market behaviour.

---

## How to Run

```bash
pip install -r requirements.txt
python volatility_forecasting_garch.py
