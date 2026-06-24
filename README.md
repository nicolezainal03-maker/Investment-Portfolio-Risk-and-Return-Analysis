# Investment-Portfolio-Risk-and-Return-Analysis
An end-to-end data science project that evaluates the balance between return and risk
in a multi-sector ETF portfolio, and benchmarks it against the broad market. The
pipeline runs Python → SQL → Tableau (no Excel) and is fully reproducible.


## Objective
Evaluate whether a sample investment portfolio strikes a sound balance between
potential return and associated risk, and whether it aligns with a company's risk
tolerance and financial objectives. The analysis produces per-asset and portfolio-level
return, volatility, Value at Risk (VaR), and risk-adjusted (Sharpe) metrics, all
compared against the S&P 500 as a benchmark.

## Tools Used
* Python
* Pandas
* yfinance
* NumPy
* SQL
* Tableau Public
* Jupyter Notebook
* R-Studio

## Methodology

### Data

The analysis covers five U.S. ETFs — **SPY** (S&P 500, used as both a holding and the
benchmark), **XLK** (tech), **XLF** (finance), **XLV** (healthcare), and **XLE**
(energy). Daily adjusted closing prices were pulled from Yahoo Finance with `yfinance`
for the years 2020-01-01 to 2025-01-01. Adjusted prices account for dividends and
stock splits. The data was checked for missing values before analysis.

### Daily returns

Daily returns are computed as the day-over-day percentage change in adjusted close:

```
r_t = (P_t / P_(t-1)) − 1
```

The first observation has no prior day and is dropped, leaving 1,257 daily returns per asset.

### Annualized return

The compounded annual growth rate, which reflects actual
compounded performance rather than a simple average:

```
Annualized Return = [ Π (1 + r_t) ] ^ (252 / n) − 1
```

where `n` is the number of daily returns and 252 is the approximate number of trading days in
a year. The daily growth factors are multiplied together to get total growth over the period,
then scaled to a single year.

### Volatility (risk)

Volatility is the annualized standard deviation of daily returns:

```
Annualized Volatility = σ_daily × √252
```

The sample standard deviation (`ddof = 1`) is used. Volatility scales with the **square root**
of time because daily returns are approximately independent, so variances add over time, and volatility is the square root of variance.

### Portfolio construction

The portfolio is **equally weighted**, holding 20% in each of the five ETFs. Its daily return
series is the weighted sum of the individual returns:

```
r_portfolio,t = Σ (w_i × r_i,t)        with w_i = 0.20 for all i
```

Annualized return and volatility are then computed on this portfolio series using the same
formulas above. Computing volatility directly on the blended series automatically captures the
**covariance** between assets(mathematically equivalent to √(wᵀ Σ w)), where Σ is the
covariance matrix. Diversification effect is reflected without averaging individual
volatilities (which would overstate risk by ignoring correlation).

### Value at Risk (VaR)

Downside risk is estimated with **parametric (variance-covariance) VaR** at 95% confidence,
assuming approximately normally distributed returns:

```
VaR (95%) = Annualized Return − (z × Annualized Volatility)        z = 1.645
```

The result is the return level that should only be breached about 1 year in 20; a negative
value represents the expected loss in such a bad year.

### Risk-adjusted return (Sharpe ratio)

The Sharpe ratio expresses excess return per unit of risk:

```
Sharpe = (Annualized Return − Risk-free Rate) / Annualized Volatility
```

A risk-free rate of **2%** is assumed as a reasonable average for short-term Treasury yields
over the 2020–2024 window. Higher Sharpe values indicate better compensation for the risk
taken.

### Benchmark comparison

Each asset and the portfolio are compared to SPY. **Beta** measures sensitivity to the market:

```
Beta = Cov(r_asset, r_SPY) / Var(r_SPY)
```

(SPY's beta against itself is 1.0 by definition, a useful internal check.) **Excess return** is
each entity's annualized return minus SPY's, and entities are flagged Outperform or
Underperform accordingly.

### Technology stack

The pipeline runs end to end across three layers: **Python / pandas** pulls the data and
computes all metrics; the results are loaded into a **SQLite** database (`portfolio.db`) as
clean tables for querying; and **Tableau** connects to the exported CSVs to build the dashboard
(risk/return scatter, cumulative return vs. benchmark, and risk by asset).
