# Nickel-And-Aluminium-Price-Reporting-2019-2026
# Two Metals, Two Broken Benchmarks
## Price Discovery Failures in Nickel and Aluminium (2019–2026)
https://kshitanjay.substack.com/p/two-metals-two-broken-benchmarks

> *Companion repository to the Substack article: "Two Metals, Two Broken Benchmarks: Why Nickel and Aluminium Need Better Price Discovery"*

[![Python](https://img.shields.io/badge/Python-3.9%2B-blue)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Data: LME](https://img.shields.io/badge/Data-LME%20Official-navy)](https://www.lme.com)
[![Data: USGS](https://img.shields.io/badge/Data-USGS%20MCS-lightgrey)](https://www.usgs.gov)

## Overview

This repository contains the data, visualisation code, and modelling scripts used in the analysis of structural price discovery failures in the LME nickel and aluminium markets. The core argument: both LME benchmarks have become increasingly disconnected from physical market reality as supply chains bifurcate along quality, geography, energy source, and carbon content — and price reporting agencies are filling the gap.

**Key findings:**

- Indonesia's share of global nickel output rose from **32.7% (2019) to 66.7% (2025e)**, the vast majority non-deliverable on the LME
- The LME cancelled **$12 billion** in nickel trades during the March 2022 crisis — driven by Class I / Class II bifurcation, not a random shock
- CRU estimates Indonesian NPI all-in sustaining costs at **$10,500–$16,000/t (2025)**, anchoring the LME price near breakeven for most producers
- The US Midwest aluminium premium surged from **$877.90/t (April 2022)** to **$1,793.99/t (October 2025)** — the latter driven by US tariffs, creating a 64% gap between LME and actual US transaction costs
- All-in US aluminium price in October 2025: **$4,580/t** vs LME price of **$2,786/t**

---

## Repository Structure

```
.
├── README.md
├── requirements.txt
├── data/
│   ├── raw/
│   │   ├── lme_nickel_cash_settlement_2019_2026.csv
│   │   ├── lme_aluminium_cash_settlement_2019_2026.csv
│   │   ├── midwest_premium_2019_2025.csv
│   │   └── usgs_nickel_world_production_2019_2025.csv
│   └── processed/
│       └── Nickel_Aluminium_Data_Workbook.xlsx
├── scripts/
│   ├── graphs.py               ← Main visualisation script (both graphs)
│   └── ou_model.py             ← Ornstein-Uhlenbeck regime estimation + Monte Carlo
└── outputs/
    ├── graph1_nickel_2019_2026.png
    └── graph2_aluminium_2019_2025.png
```

---

## Data Sources

| Dataset | Source | Coverage | Access |
|---|---|---|---|
| LME Nickel Cash-Settlement | [LME Official](https://www.lme.com/metals/ev-battery-materials/nickel) | Apr 2019 – May 2026 | Free, manual download |
| LME Aluminium Cash-Settlement | [LME Official](https://www.lme.com/metals/non-ferrous/aluminium) | Apr 2019 – May 2026 | Free, manual download |
| US Midwest Aluminium Premium | [Investing.com](https://www.investing.com/commodities/aluminium-premium-duty-paid-us-midw-futures-historical-data) | Apr 2019 – Oct 2025 | Free, downloadable CSV |
| World Nickel Mine Production | [USGS Mineral Commodity Summaries](https://www.usgs.gov/centers/national-minerals-information-center/nickel-statistics-and-information) | 2019 – 2025e | Free PDF, annual |
| NPI Cost Estimates | CRU Group via [Mining.com](https://www.mining.com/nickel-shortage-higher-costs-pinch-indonesian-producers/) | 2025 | Free article |

All data is manually transcribed from official sources and hardcoded in the scripts. No API keys required.

---

## Installation

```bash
git clone https://github.com/YOUR_USERNAME/nickel-aluminium-benchmarks.git
cd nickel-aluminium-benchmarks
pip install -r requirements.txt
```
---

## Usage
### Generate both graphs

```bash
python scripts/graphs.py
```

Outputs saved to `/outputs/`:
- `graph1_nickel_2019_2026.png` — LME Nickel price vs Indonesian production share
- `graph2_aluminium_2019_2025.png` — LME Aluminium vs Midwest Premium (with all-in price)

### Run the stochastic model

```bash
python scripts/ou_model.py
```

This script:
- Fits an Ornstein-Uhlenbeck process to LME nickel across two regimes (pre/post March 2022)
- Fits OU to LME aluminium and to the all-in US price series
- Runs Monte Carlo simulation (10,000 paths, 252-day horizon) on both aluminium series
- Prints regime parameters and 90th percentile procurement cost divergence

---
## Graphs

### Graph 1 — LME Nickel vs Indonesian Production Share (2019–2026)

![Graph 1](outputs/graph1_nickel_2019_2026.png)

The orange bars show Indonesia's share of global nickel mine output rising from 32.7% (2019) to an estimated 66.7% (2025). The navy line is the LME cash-settlement price. The red-shaded region marks the March–April 2022 crisis. The orange band is CRU's estimated NPI cost floor for Indonesian producers (2025). Note how the LME price has converged toward — and in 2025 sits barely above — the top of that cost floor.

---
### Graph 2 — LME Aluminium vs Midwest Premium (2019–2025)
![Graph 2](outputs/graph2_aluminium_2019_2025.png)

The navy line is the LME cash-settlement price. The dashed blue line is the all-in US transaction price (LME + Midwest Premium). The red bars are the Midwest Premium assessed by Fastmarkets/Platts. Two peaks are highlighted: April 2022 ($877.90/t, energy-crisis driven) and October 2025 ($1,793.99/t, US tariff driven). The growing gap between the two lines is the central argument of the article.

---

## Stochastic Modelling
### Nickel — Ornstein-Uhlenbeck Regime Break
A standard OU process for mean-reverting commodity prices:

$$dX_t = \kappa(\mu - X_t)dt + \sigma X_t dW_t$$

Where:
- $\kappa$ = speed of mean reversion
- $\mu$ = long-run mean
- $\sigma$ = volatility
- $dW_t$ = Wiener process increment

Fitted separately across two periods:

| Parameter | Pre-Crisis (2019 – Feb 2022) | Post-Crisis (Apr 2022 – May 2026) |
|---|---|---|
| $\mu$ (long-run mean, $/t) | ~$18,500 | Drifting toward $15,000–$16,000 |
| $\kappa$ (reversion speed) | Higher | Lower — slower reversion |
| Regime interpretation | Class I nickel dominates | Indonesian cost floor anchors price |

The supercycle narrative was not just directionally wrong — it was operating in a different statistical regime.

### Aluminium — Monte Carlo Procurement Risk

Two sets of OU parameters are estimated:
- **LME-only series**: mean-reverting, moderate volatility
- **All-in series** (LME + Midwest Premium): higher volatility, fat right tail

Monte Carlo simulation (10,000 paths, 252-day horizon) shows procurement cost distributions diverge materially at the 90th percentile. For a US manufacturer sourcing 50,000 tonnes annually, the tail exposure missed by LME-only hedging is significant and unhedged.

---

## Key Numbers At A Glance

| Metric | Value | Date |
|---|---|---|
| Nickel intraday peak | $101,365/t | 8 March 2022 |
| Nickel monthly avg peak | $33,298/t | April 2022 |
| Nickel low (2025) | $14,689/t | November 2025 |
| Nickel recovery | $19,128/t | May 2026 |
| LME trades cancelled | $12 billion | March 2022 |
| Indonesia share of global output | 62.3% | 2024 |
| Indonesia share (estimated) | 66.7% | 2025 |
| CRU NPI AISC range | $10,500–$16,000/t | 2025 |
| European smelter curtailments | 1.4 million tonnes | 2022 |
| Aluminium LME peak (monthly) | $3,537.85/t | March 2022 |
| Aluminium LME (May 2026) | $3,586.60/t | May 2026 |
| Midwest Premium peak (2022) | $877.90/t (~$0.40/lb) | April 2022 |
| Midwest Premium peak (2025) | $1,793.99/t (~$0.81/lb) | October 2025 |
| All-in US aluminium price | $4,580.29/t | October 2025 |

---
## Citation

If you use this data or code in your own work, please cite as:
```
Sondhi, K. (2026). Two Metals, Two Broken Benchmarks: Price Discovery Failures
in Nickel and Aluminium. Substack. https://YOUR_SUBSTACK.substack.com/p/...
```
---

## Author

**Kshitanjay Sondhi**
MSc Development Economics, London School of Economics (Distinction)
BA Economics, Delhi University (First Class, 4th of 85)
Former Economist — Risk Quantification & Model Development, S&P Global Market Intelligence

---
## License

MIT License. Data is sourced from public domain sources (LME, USGS) and third-party platforms (Investing.com, CRU via Mining.com). Use freely with attribution.
