# G10 FX Factor Strategies — carry, momentum, dollar & conditional MV portfolios

Course project for **Investments** (EPFL, MSc Financial Engineering, Group 14).
An empirical study of currency style factors in the G10 cross-section, from raw FX
data all the way to real-time, out-of-sample mean-variance portfolios.

The project builds the standard FX factor toolbox (carry, momentum, dollar), evaluates
its in-sample performance, and then constructs **conditional mean-variance efficient
(CMVE)** portfolios using only information available at each formation date — including
a Fama–MacBeth forecast of conditional means and an EWMA (RiskMetrics) conditional
covariance. Six G10 currencies vs USD (AUD, CAD, EUR, GBP, JPY, NZD), monthly,
evaluation sample **Jan 1995 – Dec 2024 (360 obs)**.

## What this demonstrates

- Building tradable **FX factors** from spot/forward rates and short rates
  (cross-sectional & time-series carry, momentum, dollar and dollar-carry).
- **Asset-pricing regressions** (spanning tests of carry on momentum + dollar).
- **Portfolio construction**: equal-weight, risk-parity and mean-variance, on both the
  raw currencies and the factor strategies, volatility-scaled for comparability.
- **Out-of-sample discipline**: EWMA conditional covariance, expanding-mean and
  **Fama–MacBeth** conditional-mean forecasts, with a constant-volatility risk target —
  i.e. estimates use only data available at formation time, no lookahead.

## Data

- **FX spot & 1M-forward** (mid) from **WRDS Datastream** (`tr_ds_equities.ds2fxrate`),
  pulled programmatically and cached locally.
- **Short-term interest rates** (OECD MEI, `IRSTCI01`) from **FRED**, monthly.

> The raw WRDS/Datastream data is licensed and is **not redistributed** in this repo.
> Reproducing the study requires your own WRDS login; the FRED series are public. See
> *Reproducing* below.

## Pipeline

`full_analysis.ipynb` runs the whole study end-to-end, in parts that mirror the report:

| Part | Topic | Output |
|---|---|---|
| 1 | Data acquisition & monthly panel | processed panel |
| 2 | Currency excess returns | Table 1 |
| 3 | Carry (CS / TS) | Table 2 |
| 4 | Momentum (CS / TS) + carry spanning regression | Table 2 |
| 5 | Dollar & dollar-carry + extended regression | Table 2 |
| 6 | In-sample portfolios (EW / RP / MV, 10% vol) | Table 3 |
| 7 | Out-of-sample CMVE portfolios (EWMA cov, expanding & FM means) | Table 4 |

## Headline results (evaluation sample, 360 obs)

**Factor strategies (Table 2):**

|  | CS-CARRY | TS-CARRY | CS-MOM | TS-MOM | DOLLAR | DOLLAR-CARRY |
|---|---|---|---|---|---|---|
| Mean (%, ann.) | 3.69 | 2.77 | 0.24 | 1.56 | −0.02 | 2.13 |
| t-stat | 2.17 | 2.57 | 0.15 | 1.29 | −0.01 | 1.54 |
| Sharpe | 0.395 | 0.469 | 0.028 | 0.236 | −0.002 | 0.282 |

**In-sample portfolios, 10% vol-scaled (Table 3):**

|  | EW-CCY | RP-CCY | MV-CCY | EW-FAC | RP-FAC | MV-FAC |
|---|---|---|---|---|---|---|
| Mean (%, ann.) | −0.02 | −0.10 | 4.65 | 4.08 | 4.15 | 5.99 |
| Sharpe | −0.002 | −0.010 | 0.465 | 0.408 | 0.415 | **0.599** |

**Out-of-sample CMVE portfolios (Table 4):**

|  | CCV-Exp | CCV-FM |
|---|---|---|
| Mean (%, ann.) | 2.13 | 6.18 |
| t-stat | 0.99 | 2.82 |
| Vol (%, ann.) | 11.83 | 12.00 |
| Sharpe | 0.180 | **0.515** |

The Fama–MacBeth conditional-mean forecast (CCV-FM) is the standout: a **0.52 Sharpe
out-of-sample**, in real time, well ahead of the naive expanding-mean benchmark — the
predictive content of the carry and momentum signals survives an honest OOS test.

## Repository contents

```
investments-fx-factors/
├── full_analysis.ipynb   # end-to-end study (Parts 1–7), the main deliverable
├── report.tex            # LaTeX report with full tables, figures and discussion
├── requirements.txt      # numpy, pandas, statsmodels, wrds, ...
└── data/  output/        # regenerated locally when the notebook runs (not shipped)
```

## Reproducing

```bash
pip install -r requirements.txt
```

1. **WRDS** — set `wrds_username='your_wrds_username'` in the Part 1 cell to your own
   login. The daily FX panel is cached to `data/raw/fx_rates_raw.csv` on first run.
2. **FRED** — download each `IRSTCI01*` series as a **monthly** CSV from
   fred.stlouisfed.org into `data/raw/fred/` (USD, AUD, CAD, GBP, JPY, NZD, plus DE
   pre-1999 and EZ from 1999 for the EUR leg).
3. Run `full_analysis.ipynb` top to bottom; all tables and figures regenerate under
   `data/processed/` and `output/`.

## Team

Group 14 — **Alexandre Vallet, Leonard Rambeau, Victor Legrand, Loric Rey**.
