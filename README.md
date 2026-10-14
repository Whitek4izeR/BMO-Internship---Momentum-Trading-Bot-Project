# Small-Cap EMA + RSI + Volume Strategy (TradingView)
- Momentum Trading Bot Project
- Momentum Trading Bot Project on Tradingview for Small Cap Stocks with Backtesting


A TradingView strategy written in Pine Script v6 that trades EMA crossovers on small-cap stocks, with RSI, volume, and liquidity filters plus ATR-based stop-loss and take-profit.

> **Status:** Version 1. Backtest-ready, not yet validated across many stocks or market periods. See [Limitations](#limitations).

---

## Table of Contents

- [How it works](#how-it-works)
- [Features](#features)
- [Settings](#settings)
- [How to use](#how-to-use)
- [Backtesting guide](#backtesting-guide)
- [Results](#results)
- [Alerts and automation](#alerts-and-automation)
- [Limitations](#limitations)
- [Roadmap](#roadmap)
- [Disclaimer](#disclaimer)
- [License and credits](#license-and-credits)

---

## How it works

The strategy follows a simple idea: **trade when the trend turns, but only if other signals agree.**

| Step | Rule |
| --- | --- |
| 1. Signal | Fast EMA (9) crosses the slow EMA (21). Cross up means a buy signal. Cross down means a sell signal. |
| 2. RSI check | RSI must agree with the direction and must not be stretched (for longs: between 50 and 70). |
| 3. Volume check | The current bar's volume must be at least 1.2x its recent average. |
| 4. Liquidity check | The stock must trade enough dollar volume per bar to enter and exit easily. |
| 5. Risk control | Each trade gets an automatic stop-loss (2x ATR) and take-profit (3x ATR). |
| 6. Exit | The opposite EMA cross also closes the position. |

**EMA** (Exponential Moving Average) is a smoothed average price. **RSI** (Relative Strength Index) measures how hard price has been pushing up or down on a 0-100 scale. **ATR** (Average True Range) is how far the stock typically moves per candle.

## Features

- EMA crossover entries with adjustable lengths
- RSI confirmation with adjustable thresholds (strict or loose)
- Volume and liquidity filters designed for thinly traded stocks
- Optional long-term trend filter (200 EMA, off by default)
- ATR-based stop-loss and take-profit
- Long and short support (**short is off by default**, because shorting small caps can mean borrow fees and sudden squeezes)
- Commission and slippage built into the backtest for more realistic results
- Alert messages for entries and exits

## Settings

All settings are in the strategy's **Settings > Inputs** tab.

| Setting | Default | What it does |
| --- | --- | --- |
| Trade LONG / Trade SHORT | On / Off | Choose which sides to trade |
| Fast / Slow EMA Length | 9 / 21 | Crossover speed |
| Use RSI confirmation | On | Turns the RSI filter on or off |
| RSI Length | 14 | Number of bars RSI looks at |
| Long: RSI above / skip above | 50 / 70 | RSI range allowed for buys |
| Short: RSI below / skip below | 50 / 30 | RSI range allowed for sells |
| Use volume filter | On | Requires above-average volume |
| Volume multiplier | 1.2 | How much above average is required |
| Use liquidity filter | On | Skips thinly traded bars |
| Min average $ traded per bar | 50,000 | Lower this if you see no trades |
| Use trend filter | Off | Only buy above / short below the trend EMA |
| Trend EMA Length | 200 | Length of the trend EMA |
| Use ATR stop-loss / take-profit | On | Automatic exits |
| Stop-loss / Take-profit (x ATR) | 2.0 / 3.0 | Distance of the exits |
| Send alerts | On | Turns alert messages on or off |

Strategy-level settings (in the script's first line): initial capital of 100,000, 10% of equity per trade, 0.05% commission, 5 ticks of slippage.

## How to use

1. Open [TradingView](https://www.tradingview.com) and load a small-cap stock chart (for example, `NASDAQ:CWCO`).
2. Set the chart type to plain **Candles** (other chart types give unrealistic backtests).
3. Open the **Pine Editor** at the bottom of the screen.
4. Copy the contents of `smallcap_ema_rsi_strategy.pine` and paste it in.
5. Click **Add to chart**.
6. Open the **Strategy Tester** tab to view results.

**Recommended timeframes:** 1h, 4h, or Daily. Very short timeframes (1-5 minutes) produce many false crossovers.

**If you see "This report requires trade data":** the strategy placed no trades. Common causes are order size rounding down to zero (raise initial capital for high-priced symbols) or filters that are too strict (lower the liquidity threshold or untick the volume filter).

## Backtesting guide

A single backtest on one stock proves very little. To test properly:

- Test on **10-20 different small caps**, not just one.
- Include **different market periods**, including a bad one.
- **Tune on one period, then check on a period the strategy has never seen** (out-of-sample testing).
- Aim for **100+ trades** per test. A result from 10 trades is not reliable.
- Compare against **buy-and-hold**.
- Record the same metrics every time: net profit, max drawdown, win rate, profit factor, number of trades.
- Do not keep adjusting settings until the numbers look perfect. That is overfitting, and it usually fails on new data.

## Results

> Fill this table in with your own test results. The numbers below are placeholders, not real results.

| Stock | Timeframe | Period | Net profit | Max drawdown | Win rate | Profit factor | # of trades | vs. Buy and hold |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [e.g. CWCO] | [1h] | [dates] | | | | | | |
| | | | | | | | | |

## Alerts and automation

The script includes `alert()` messages for entries and exits. To use them in TradingView, create an alert on the strategy and choose **"Order fills and alert() function calls"**.

**Plan requirements:** Strategy alerts and webhook notifications may require a paid TradingView plan. The free Basic plan appears to offer no strategy alerts and no webhooks. Check the current plan details at tradingview.com/pricing.

**This strategy does not place real orders by itself.** Automated trading needs a webhook connected to a broker or connector service. Always validate with paper trading first.

## Limitations

- **One symbol at a time.** TradingView strategies cannot scan or rank a whole small-cap universe.
- **Survivorship bias.** Charts mostly show companies that still exist, so failed small caps are missing and results can look better than reality.
- **Small-cap risks.** Wide spreads, overnight gaps (which can jump past a stop-loss), thin trading, and pump-and-dump moves can all make real results worse than a backtest.
- **Trend-following, not pure momentum.** An EMA crossover tells you the trend has turned. It does not detect stocks that are already moving fast.
- **No portfolio-level risk rules.** Daily loss limits and maximum open positions across stocks are not possible in a single script.
- **Not independently verified.** Results should be confirmed across multiple stocks and periods before being trusted.

## Roadmap

- [x] Version 1: EMA crossover with RSI, volume, liquidity filters, and ATR exits
- [ ] Version 2: true momentum entry (breakout above recent highs plus relative volume)
- [ ] Compare Version 2 against Version 1 on the same watchlist
- [ ] Optional: earnings-date filter
- [ ] Optional: paper trading through a connected broker

## Disclaimer

This project is for **educational and research purposes only**. It is not financial advice. Backtested results do not guarantee future performance, and trading small-cap stocks involves significant risk, including the loss of your capital. Do your own research and consult a licensed financial professional before trading real money.

