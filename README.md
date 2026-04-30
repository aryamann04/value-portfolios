# value-portfolios

Fundamental factor backtesting tool for constructing and analyzing quintile portfolios on the NASDAQ 100, Russell 200, and S&P 500.

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=flat&logo=pandas&logoColor=white)
![NumPy](https://img.shields.io/badge/NumPy-013243?style=flat&logo=numpy&logoColor=white)
![Plotly](https://img.shields.io/badge/Matplotlib-3F4F75?style=flat&logo=plotly&logoColor=white)

## Overview

value-portfolios ranks index constituents by any of 69 fundamental financial metrics, splits them into quintile portfolios, and backtests those portfolios across a configurable date range. At each rebalance date the tool re-ranks constituents using point-in-time data, so there is no look-ahead bias. Portfolios can be equally weighted or market-cap weighted, and rebalancing can occur yearly, quarterly, or monthly.

Historical constituent membership, fundamental metrics, and daily price data were sourced from CRSP and Compustat via Wharton Research Data Services (WRDS), covering **June 2000 – June 2024**. A separate regression module lets you test the statistical relationship between any metric and forward stock returns.

## Features

- **Quintile construction** — ranks all index members by a chosen metric and splits into 5 portfolios plus a negative-value bucket
- **Full-period backtesting** — chains rebalance periods from `start_date` to `end_date`, accumulating daily returns with no look-ahead bias
- **Equal-weight and market-cap-weight modes** — selectable per run
- **Flexible rebalancing** — yearly, quarterly, or monthly
- **Performance analysis** — per-portfolio CAGR, annual return heatmap, and comparison against the live index via yfinance
- **Regression analysis** — OLS regression of stock returns on one or multiple metrics; batch mode ranks all 69 metrics by R²
- **Large-cap index construction** — reconstructs a market-cap-weighted S&P 500 proxy from historical constituent lists, rebalanced monthly

## Project Structure

```
value-portfolios/
├── main.py               # Entry point — set parameters and run
├── backtest.py           # Quintile ranking, return calculation, rebalancing loop
├── datapipeline.py       # Data access: constituents, historical metrics, prices
├── portfolioanalysis.py  # CAGR calculation, annual return heatmap, index comparison
├── largecapindex.py      # Market-cap-weighted S&P 500 proxy construction
├── print.py              # Cumulative return plots, data resampling, metric labels
├── regression.py         # OLS regression of returns on fundamental metrics
└── files/
    ├── nasdaq/           # NASDAQ 100 prices, fundamentals, constituents, tickers
    └── russell200/       # Russell 200 prices, fundamentals, constituents, tickers
```

## Setup

**Prerequisites**: Python 3.8+

```bash
pip install pandas numpy matplotlib seaborn yfinance scipy statsmodels
```

Data files are stored under `files/`. The S&P 500 data files are not included in the repository and must be placed at the paths referenced in `datapipeline.py`.

## Usage

Configure parameters at the top of `main.py` and run:

```bash
python main.py
```

```python
# main.py — configurable parameters
metric             = 'pe_exi'    # any metric key from the table below
start_date         = '2003-01-01'
end_date           = '2024-01-01'
index              = 'nasdaq100' # 'nasdaq100' | 'russell200' | 'sp500'
market_cap_weight  = False       # True for market-cap weighting
rebalance_frequency = 'yearly'  # 'yearly' | 'quarterly' | 'monthly'
plot_granularity   = 'quarterly' # 'daily' | 'quarterly' | 'yearly'
```

**Output**: portfolio statistics printed to console, an annual return heatmap, and a cumulative return chart with all six portfolios overlaid against the benchmark index.

**Regression analysis**:

```python
from regression import regression, all_metrics_regression

# Single metric OLS regression
regression('nasdaq100', ['npm'], '2003-01-01', '2024-01-01')

# Rank all 69 metrics by predictive power (R²)
all_metrics_regression('nasdaq100', '2003-01-01', '2024-01-01')
```

## Available Metrics

| Key | Description |
|-----|-------------|
| `pe_exi` | P/E (Diluted, Excl. EI) |
| `pe_inc` | P/E (Diluted, Incl. EI) |
| `pe_op_basic` | Price/Operating Earnings (Basic, Excl. EI) |
| `pe_op_dil` | Price/Operating Earnings (Diluted, Excl. EI) |
| `ps` | Price/Sales |
| `pcf` | Price/Cash Flow |
| `ptb` | Price/Book |
| `bm` | Book/Market |
| `capei` | Shiller Cyclically Adjusted P/E |
| `evm` | Enterprise Value Multiple |
| `peg_trailing` | Trailing PEG Ratio |
| `divyield` | Dividend Yield |
| `dpr` | Dividend Payout Ratio |
| `npm` | Net Profit Margin |
| `gpm` | Gross Profit Margin |
| `opmad` | Operating Profit Margin After Depreciation |
| `opmbd` | Operating Profit Margin Before Depreciation |
| `ptpm` | Pre-tax Profit Margin |
| `cfm` | Cash Flow Margin |
| `roe` | Return on Equity |
| `roa` | Return on Assets |
| `roce` | Return on Capital Employed |
| `aftret_eq` | After-tax Return on Avg. Common Equity |
| `aftret_equity` | After-tax Return on Total Stockholders Equity |
| `aftret_invcapx` | After-tax Return on Invested Capital |
| `pretret_earnat` | Pre-tax Return on Total Earning Assets |
| `pretret_noa` | Pre-tax Return on Net Operating Assets |
| `gprof` | Gross Profit/Total Assets |
| `efftax` | Effective Tax Rate |
| `de_ratio` | Total Debt/Equity |
| `debt_assets` | Total Debt/Total Assets |
| `debt_at` | Total Debt/Total Assets (alt.) |
| `debt_capital` | Total Debt/Capital |
| `debt_ebitda` | Total Debt/EBITDA |
| `debt_invcap` | Long-term Debt/Invested Capital |
| `totdebt_invcap` | Total Debt/Invested Capital |
| `dltt_be` | Long-term Debt/Book Equity |
| `lt_debt` | Long-term Debt/Total Liabilities |
| `lt_ppent` | Total Liabilities/Total Tangible Assets |
| `capital_ratio` | Capitalization Ratio |
| `equity_invcap` | Common Equity/Invested Capital |
| `intcov` | After-tax Interest Coverage |
| `intcov_ratio` | Interest Coverage Ratio |
| `int_debt` | Interest/Avg. Long-term Debt |
| `int_totdebt` | Interest/Avg. Total Debt |
| `curr_ratio` | Current Ratio |
| `quick_ratio` | Quick Ratio (Acid Test) |
| `cash_ratio` | Cash Ratio |
| `cash_lt` | Cash Balance/Total Liabilities |
| `cash_debt` | Cash Flow/Total Debt |
| `cash_conversion` | Cash Conversion Cycle (Days) |
| `ocf_lct` | Operating CF/Current Liabilities |
| `fcf_ocf` | Free Cash Flow/Operating Cash Flow |
| `profit_lct` | Profit Before Depreciation/Current Liabilities |
| `curr_debt` | Current Liabilities/Total Liabilities |
| `short_debt` | Short-Term Debt/Total Debt |
| `at_turn` | Asset Turnover |
| `inv_turn` | Inventory Turnover |
| `rect_turn` | Receivables Turnover |
| `pay_turn` | Payables Turnover |
| `sale_equity` | Sales/Stockholders Equity |
| `sale_invcap` | Sales/Invested Capital |
| `sale_nwc` | Sales/Working Capital |
| `invt_act` | Inventory/Current Assets |
| `rect_act` | Receivables/Current Assets |
| `accrual` | Accruals/Average Assets |
| `rd_sale` | R&D/Sales |
| `adv_sale` | Advertising Expenses/Sales |
| `staff_sale` | Labor Expenses/Sales |

## Example Results

20-year backtest of equally-weighted, yearly-rebalanced portfolios ranked by P/E on the NASDAQ 100 (2003–2024):

<img width="900" alt="Cumulative returns by P/E quintile" src="https://github.com/aryamann04/fundamental-portfolios/assets/140534650/1d675bbc-cb0b-4c5d-802e-318d19c9e425">

<img width="900" alt="Annual return heatmap" src="https://github.com/aryamann04/fundamental-portfolios/assets/140534650/725a44ab-5544-4585-b42f-fadc5ce43315">

Univariate OLS regression of NASDAQ 100 constituent returns on Net Profit Margin:

<img width="700" alt="NPM regression scatter" src="https://github.com/user-attachments/assets/1bd5600d-f5af-4d6d-8cb5-116ab21a58a2">
<img width="300" alt="NPM regression results" src="https://github.com/user-attachments/assets/0cd01bd5-39ec-499a-89ae-5c72b12b72f2">
