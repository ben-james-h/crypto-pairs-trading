# Crypto Pairs Trading — Statistical Arbitrage Research

A research project testing pairs-trading strategies on cryptocurrency markets,  comparing four selection methodologies of increasing sophistication. Each notebook isolates a single design decision against the one before it, so any performance change is attributable to that change rather than to parameter tuning.

## Repository layout

```
.
├── README.md
├── .gitignore
├── LIMITATIONS_AND_NEXT_STEPS.md
├── data_pipeline/
│   └── crypto_price_volume_fetcher.ipynb     # Downloads OHLCV from Binance
└── reversal/
    ├── 01_correlation_static.ipynb           # Correlation-based pair selection
    ├── 02_cointegration_static.ipynb         # Engle–Granger + half-life filter
    ├── 03_cointegration_rolling_beta.ipynb   # Rolling hedge ratio
    └── 04_cointegration_dynamic_pairs.ipynb  # Walk-forward pair re-selection
```

The cache directories (`data_cache/`, `data_output/`) are generated at runtime and excluded from version control.

## How to run

1. Run `data_pipeline/crypto_price_volume_fetcher.ipynb` first. This downloads all active Binance USDT spot pairs from 2022-01-01 onwards and writes consolidated price and volume pickles to `data_output/`.
2. Run the strategy notebooks in `reversal/` *in order*. They are progressive and the markdowns from one strategy (e.g. explaining half-life mean reversion) are not then copied across to the next. 

## Methodology

All four strategies share the same backtest scaffolding — daily bars, 180-day rolling z-score, entry at |z|>2.5, exit at |z|<0.5 (these parameters are chosen to be conservative), gross exposure normalised to 1, flat 20 bps and liquidity-aware cost models, full diagnostics against BTC buy-and-hold. The only thing that varies between notebooks is **how pairs are selected and how the hedge ratio is estimated**:

| # | Notebook | Pair selection | Hedge ratio |
|---|---|---|---|
| 01 | Correlation (static) | Return correlation > 0.8 on training window | Static OLS, fit once |
| 02 | Cointegration (static) | Two-way Engle–Granger p<0.01 + half-life 4–20 days | Static OLS, fit once |
| 03 | Rolling β | Static (as 02) | Rolling OLS |
| 04 | Dynamic pairs | Walk-forward EG every N months on trailing window | Static within each segment |

The progression isolates one design choice at a time — selection criterion (01 → 02), hedge-ratio dynamism (02 → 03), and pair-set dynamism (02 → 04). Each notebook holds every other parameter identical to its predecessor.

## Key findings

**Static cointegration with a half-life filter is the strongest strategy tested, yet clearly overfits the training window.** Full-sample net Sharpe 5.14 at 20 bps, max drawdown −8.7%, drawdown duration 107 days, alpha t-stat 9.7 against BTC with effectively zero beta (−0.002) and residual correlation of 0.004.

**Rolling beta hurts performance.** Rolling β underperforms static β across every window length tested (30, 60, 90, 120, 180, 365 days). 

**Dynamic pair selection removes overfitting on training window, since this is not applicable and still achieves target metrics** Dynamic pair selection generates sufficient net sharpe, alpha t-stat etc. metrics when the lookback window is at least 12 months.

**Correlation selection alone is meaningfully weaker than cointegration.** Correlation finds pairs that move together; it doesn't test whether their combination is stationary. Switching the selection criterion lifts full-sample net Sharpe from 1.19 (correlation) to 5.14 (cointegration), with the rest of the pipeline held constant.

## Honest caveats

The static cointegration notebook's in-sample Sharpe of 7.5 is flattered by selecting and fitting pairs on the same window — a form of in-sample optimism. The out-of-sample Sharpe of 2.5 is the more representative number. The dynamic-pairs notebook (04) eliminates this in-sample / out-of-sample distinction entirely by walking selection forward through the whole sample, and produces more realistic full-sample Sharpes in the 1.5–2.6 range.

A discussion of limitations and potential next steps for future work sits in: [`LIMITATIONS_AND_NEXT_STEPS.md`](./LIMITATIONS_AND_NEXT_STEPS.md).
