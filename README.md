# Yen Carry Trade Stress Index

A PCA-weighted composite index that tries to quantify stress from yen carry trade unwinds, built from daily Japan/US interest rate, FX, and volatility data (2000 – Feb 2026).

- [Presentation Slides](https://docs.google.com/presentation/d/1jkSqfUzfLEzkBsTPcSmUb2oV5hljNnn5a7Bv3CQIMR4/edit?usp=sharing)

## Overview

In a yen carry trade, investors borrow yen at very low rates and invest in higher-yielding assets abroad. When the yen strengthens or Japanese rates rise, those positions get unwound and the shock can spread across global markets (e.g., August 2024 and January 2026). No direct measure of this exists, so this project builds a proxy and asks:

1. Do USD/JPY, Japanese Government Bond (JGB) yields, US–Japan rate differentials, and the CBOE VIX correlate with one another?
2. Can a daily PCA-weighted composite index of these variables **detect** the August 2024 and January 2026 unwinds?
3. Does the index rise **before** those events (predictive value)?

## Approach

1. **Collect and clean** six public datasets (below), flagging missing values and outliers for each.
2. **Merge** on date (holidays forward-filled) into one daily table of 6,798 rows and nine variables: BOJ overnight call rate, Fed funds rate, VIX close, USD/JPY, JGB 10Y and 30Y yields, US 10Y yield, and the US–Japan 10Y and policy-rate spreads.
3. **Explore** pairwise correlations and OLS regressions against the VIX.
4. **Build the index:** run PCA on the standardized variables (the first four components explain over 90% of variance), blend the four sets of loadings weighted by explained variance, and project the data onto the blend, scaled to 0–100.
5. **Evaluate** the index against the two unwind events.

## Repository Structure

```
├── 00-ProjectProposal.ipynb   # Research question, hypotheses, data plan, ethics
├── 01-DataCheckpoint.ipynb    # Downloads raw data; cleans each dataset
├── 02-EDACheckpoint.ipynb     # Correlation analysis; saves figures to results/
├── 03-FinalProject.ipynb      # Merged dataset, PCA, stress index, conclusions
├── modules/get_data.py        # Helper for downloading raw CSVs
├── results/                   # EDA figures (PNG)
└── data/                      # 00-raw, 01-interim, 02-processed (git-ignored)
```

## Data

| Series | Source |
|---|---|
| JGB 10Y and 30Y yields | Japan Ministry of Finance |
| USD/JPY spot rate (DEXJPUS) | FRED |
| US 10Y Treasury yield (DGS10) | FRED |
| Federal funds effective rate | FRED |
| Uncollateralized overnight call rate | Bank of Japan |
| VIX (daily OHLC) | CBOE, via Yahoo Finance |

`01-DataCheckpoint.ipynb` pulls the raw CSVs from published Google Sheets copies of these sources.

## Getting Started

```bash
git clone https://github.com/BJKin/Yen_Carry_Stress_Index.git
cd Yen_Carry_Stress_Index
pip install pandas numpy matplotlib seaborn scikit-learn statsmodels scipy requests tqdm jupyter
```

Run the notebooks in order. Raw and processed data are git-ignored, so `01` and `02` generate the files that `03` loads:

1. `01-DataCheckpoint.ipynb` downloads the raw data into `data/00-raw/` and writes cleaned CSVs to `data/02-processed/`.
2. `02-EDACheckpoint.ipynb` writes the merged yield dataset and the figures in `results/`.
3. `03-FinalProject.ipynb` builds the stress index and produces the final results.
