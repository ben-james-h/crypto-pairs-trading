## Limitations & Next Steps

A companion to the strategy notebooks and the README, documenting any limitations in the strategies and potential next steps to improve this project or for other projects.

# Limitations

- Transaction costs are assumed to be a flat 20bps, or we utilise a liquidity-aware model which does not account for wider spreads for less liquid altcoins.
- We assume that shorting is frictionless.
- Data subject to selection bias at two levels. Firstly, due to the 90% coverage filter and secondly since delisted coins are absent from the Binance API entirely; Binance does not serve historical data for delisted pairs, so this cannot be remedied without a paid data source such as Kaiko. Both levels potentially flatter results.
- Full period (2022-01-01 to 2026-05-22) covers several market regimes.
- Volume data not embedded into the alpha signal. 

# Next Steps

1. Test on higher-frequency data, since we have only covered this on "1d" data.
2. I also tested an Accumulated Swing Index/MA Trend filter on the static cointegration strategy, purely out of interest, but I was conscious of overfitting (two additional filters) and noted that this worsened results.
3. Consider using a Kalman filter to estimate β.
4. I want to explore positiomning based on perp. futures funding rates as a separate strategy, but the data is more difficult to obtain here. 
5. RSI/Bollinger bands as an execution overlay on the spread.
6. Continuoous z-score signal sizing and the impact on results.
7. Incorporate volume into the alpha signal directly — either as a position-sizing scalar (e.g. scale down exposure when either leg has volume below its trailing average) or as an entry gate.
8. Supplement strategy with a momentum-based strategy.
