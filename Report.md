# Investment Portfolio Risk & Return Analysis — Findings
**Analysis period:** 2020-01-02 to 2024-12-31 (1,258 trading days · 1,257 daily returns)
**Portfolio:** equal weight, 20% each in SPY, XLK, XLF, XLV, XLE
**Benchmark:** SPY · **Risk-free assumption:** 2% annual · **VaR confidence:** 95% (z = 1.645)
**Pipeline:** Yahoo Finance (yfinance) → Python / pandas → SQLite → Tableau
---
## Summary metrics
| Entity | Sector | Ann. Return | Ann. Volatility | VaR 95% (annual) | Sharpe | Beta vs SPY | vs SPY |
|--------|--------|------------:|----------------:|-----------------:|-------:|------------:|--------|
| SPY | S&P 500 (benchmark) | 14.28% | 21.00% | −20.26% | 0.58 | 1.00 | Benchmark |
| XLK | Technology | **21.16%** | 27.96% | −24.82% | **0.69** | 1.24 | Outperform (+6.89%) |
| XLF | Financials | 11.43% | 26.36% | −31.94% | 0.36 | 1.06 | Underperform (−2.85%) |
| XLV | Healthcare | 7.86% | **18.34%** | −22.31% | 0.32 | **0.72** | Underperform (−6.42%) |
| XLE | Energy | 12.04% | **36.41%** | **−47.85%** | **0.28** | 1.04 | Underperform (−2.24%) |
| **PORTFOLIO** | Equal-weight | **14.52%** | 22.28% | −22.13% | 0.56 | 1.01 | Outperform (+0.24%) |
*Bold marks the best/worst value in each column. A negative annual VaR is the loss expected in
a bad year (roughly the worst 1 year in 20).*
## Reading the dashboard
The three views were built to be read together, each answering a different question.
**The risk/return map (scatter).** Plotting annualized return against volatility (colored by
benchmark status, sized by Sharpe) shows where each holding earns its keep. The ideal position
is up to the left (more return, less risk). **XLK sits highest** and is the largest point because its
Sharpe is best; whereas **XLE is pushed far to the right** meaning very risky for only a middling height; 
and **XLV hugs the left edge**, which means it has a low-risk but low-return. SPY and the portfolio cluster 
together in the middle.
**Cumulative return vs. benchmark (line).** Tracking the growth of $10,000 from 2020 to 2024,
the portfolio and SPY both absorb the COVID downturn in early 2020 but then recover together. 
SPY leads through 2021, but the portfolio pulls ahead from roughly 2022 onward and finishes slightly
higher (~$20.5K vs ~$19.5K). The visual confirms that the portfolio's 14.52% is edging out
SPY's 14.28%.
**Risk by asset (bars).** Volatility is ranked from high to low and shaded by VaR. This makes the
concentration of risk obvious: **XLE towers over everything** (~36% volatility, darkest shade
for its −47.85% downside), while **XLV is shortest and lightest**. 
## What the numbers say
**Technology (XLK) is the clear standout.** Highest annualized return (21.2%) and best
risk-adjusted return (Sharpe 0.69), beating the benchmark by 6.9 points a year. However, 
its volatility is elevated (28%) and its beta of 1.24 amplifies market moves.
**Energy (XLE) is the weakest on a risk-adjusted basis.** XLE has the highest volatility (36%) and
the worst downside ( a 95% annual VaR of −47.85%), meaning a bad year could plausibly erase nearly
half its value with only a 12% return.
**Healthcare (XLV) is the stabilizer.** Lowest volatility (18%), lowest beta (0.72), and smallest downside. 
The trade-off is the lowest return (7.9%).
**Financials (XLF)** underperformed the benchmark while carrying fairly high volatility.
**The portfolio essentially tracks the index.** Equal-weighting the five ETFs produced 14.52%
versus SPY's 14.28% at higher volatility (22.3% vs 21.0%). The net effect is a Sharpe ratio slightly *below* 
simply holding SPY (0.56 vs 0.58).
---
*Reproducible end to end: the data pull, metric calculations, and SQLite load run in the project
notebook; the three views and dashboard are built in Tableau from the exported CSVs
(`summary_metrics.csv`, `cumulative_returns_long.csv`).*
