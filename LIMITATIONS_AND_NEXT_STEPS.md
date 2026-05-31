## Limitations & Next Steps

A companion to the strategy notebooks and the README, documenting any limitations in the strategies and potential next steps to improve this project or for other projects.

# Limitations

- Transaction costs are assumed to be a flat 20bps, or we utilise a liquidity-aware model which does not account for wider spreads for less liquid altcoins.
- We assume that shorting is frictionless.
- Data subject to survivorship bias, since based on 90% coverage on the training window.
- Full period (2022-01-01 to 2026-05-22) covers several market regimes.

# Next Steps

1. Test on higher-frequency data, since we have only covered this on "1d" data.
2. I also tested an Accumulated Swing Index/MA Trend filter on the static cointegration strategy, purely out of interest, but I was conscious of overfitting (two additional filters) and noted that this significantly worsened results.
3. Consider using a Kalman filter to estimate β.
4. I want to explore positiomning based on perp. futures funding rates as a separate strategy, but the data is more difficult to obtain here. 
5. RSI/Bollinger bands as an execution overlay on the spread.
6. Continuoous z-score signal sizing and the impact on results.
7. Dynamic volatility-regime gating, supplemented with a momentum-based strategy.