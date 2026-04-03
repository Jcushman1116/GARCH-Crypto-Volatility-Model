# Bitcoin Volatility Modeling — ARCH and GARCH

## Overview

Models conditional volatility in Bitcoin daily returns using ARCH and GARCH frameworks.
Data is pulled live from `yfinance` for BTC-USD and ETH-USD, though Ethereum is excluded
from final modeling after failing the ARCH LM test for conditional heteroskedasticity.
The analysis fits and compares volatility models on both squared returns and squared log
returns, validates residuals, and identifies the best-fitting specification for each series.

## Methodology

Daily closing prices for BTC-USD and ETH-USD are pulled from `yfinance`. Returns, log
returns, and their squared equivalents are computed for each ticker. Missing values are
back-filled on a daily frequency.

The modeling pipeline follows four stages:

**1. Stationarity testing** — The Augmented Dickey-Fuller test is applied to all return
series. All series pass, confirming variance stationarity required for ARCH/GARCH modeling.

**2. Conditional heteroskedasticity testing** — The ARCH Lagrange Multiplier test is
applied to each series. Ethereum fails to reject the null hypothesis and is excluded.
BTC squared returns and squared log returns proceed to modeling.

**3. ACF/PACF analysis** — Autocorrelation and partial autocorrelation plots are
examined across 40 lags to inform model order selection.

**4. Model fitting and selection** — ARCH($p$) and GARCH($p$,$q$) models are fit using
the `arch` library. Model order is selected using a combination of AIC/BIC values and
the Log-Likelihood Ratio (LLR) test:

$$
LR = 2(\ell_2 - \ell_1) \sim \chi^2(\Delta k)
$$

If $p < 0.05$, the more complex model is preferred. Final models selected:

- **Squared returns:** GARCH(1,1)
- **Squared log returns:** ARCH(3)

Model fit is evaluated by plotting conditional volatility against realized volatility,
computing Mean Squared Error (MSE), and running the Ljung-Box test on raw, standardized,
and squared standardized residuals to confirm no remaining autocorrelation.

## Language

Python

Packages:

- `numpy`
- `pandas`
- `matplotlib`
- `seaborn`
- `scipy`
- `statsmodels`
- `arch`
- `scikit-learn`
- `yfinance`

## How to Run

1. Clone the repository
2. Install dependencies: `pip install -r requirements.txt`
3. Open `Crypto_Vol_model.ipynb` in Jupyter
4. Adjust `start` and `end` in Cell 12 to set the analysis window
5. Run all cells in order — data is pulled live from `yfinance` on execution
