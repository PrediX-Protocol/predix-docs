---
description: Read prediction market charts, change timeframes, and apply indicators.
---

# Chart & timeframe

Prediction market charts are not crypto charts. The Y-axis is bounded between $0 and $1 — a probability, not an unbounded asset price. PrediX renders prediction market data accurately while preserving the tooling traders expect from professional interfaces.

### Understanding the chart

The chart aggregates prices from three on-chain sources into a single canonical YES price / time series:

<table data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><mark style="color:orange;"><strong>Router.Trade</strong></mark></td><td>canonical source, every market order.</td></tr><tr><td><mark style="color:orange;"><strong>Hook AMM swaps</strong></mark></td><td>tick-by-tick AMM prices for analytics.</td></tr><tr><td><mark style="color:orange;"><strong>CLOB matches</strong></mark></td><td>price at each limit fill.</td></tr></tbody></table>

![Chart data: 3 sources (Router.Trade canonical + Hook AMM analytics + CLOB matches) combined into YES price chart](../../.gitbook/assets/41-chart-sources.svg)

All three feed into a unified combined chart on the _<mark style="color:orange;">market detail page</mark>_.

#### Reading OHLC candles

Each candle shows the **open / high / low / close** for that period:

```
─┬─ high
 │
 │  close (green if close > open)
 ┃
 │  open
 │
─┴─ low
```

Volume is displayed as bars at the bottom of the chart.

### Timeframes

PrediX supports six timeframes, each suited to a different style of trading.

| Timeframe   | Use case                           |
| ----------- | ---------------------------------- |
| **1m**      | Scalp, intraday                    |
| **5m**      | Short-term momentum                |
| **15m, 1h** | Day trade                          |
| **4h**      | Swing                              |
| **1D**      | Position trade, multi-day hold     |
| **1W**      | Long market (3+ months to endTime) |

The app picks a sensible default based on time-to-end:

* < 24h to endTime → 5m
* 1-7 days → 15m or 1h
* 7 days → 1h or 4h
* 30 days → 1D

### Indicators

Toggle indicators in the chart settings (gear icon):

<table data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><mark style="color:orange;"><strong>MA / EMA</strong></mark></td><td>moving average over 7, 25, 99 periods.</td></tr><tr><td><mark style="color:orange;"><strong>VWAP</strong></mark> </td><td>volume-weighted average price.</td></tr><tr><td><mark style="color:orange;"><strong>Bollinger Bands</strong></mark></td><td>volatility envelope.</td></tr><tr><td><mark style="color:orange;"><strong>RSI</strong></mark></td><td>momentum oscillator (0-100).</td></tr><tr><td><mark style="color:orange;"><strong>Volume profile</strong></mark></td><td>volume distribution by price.</td></tr></tbody></table>

### Compare two markets

Click **Compare** then select another market. The chart overlays two YES price lines on the same axis (normalized 0-1, since both are already probabilities).

Useful pairings include "Trump win" vs "Biden win" during the 2024 election cycle.

### Multi-outcome event chart

In the event detail view, the chart shows the YES price of **all members** on the same timeline. For example, _"FIFA WC 2026 Winner"_ over six months:

| Month | Argentina | Brazil | France |
| ----- | --------- | ------ | ------ |
| Jan   | $0.20     | $0.18  | $0.15  |
| Feb   | $0.22     | $0.16  | $0.18  |
| Mar   | $0.25     | $0.15  | $0.22  |
| Apr   | $0.28     | $0.14  | $0.20  |
| May   | $0.32     | $0.12  | $0.18  |
| Jun   | $0.35     | $0.10  | $0.15  |

In the app, a line chart overlays all members. Click a member in the legend to highlight or hide. Useful for tracking probability shifts in real time.

### Order book depth

The **Depth** tab next to the chart shows:

* Bids (BUY orders) on the left, asks (SELL orders) on the right.
* Cumulative volume as walls of liquidity.

Useful for spotting a **liquidity wall** — a large limit order that may stall the price.

### Recent trades

The **Trades** tab lists every trade in real time:

* Side, size, price, timestamp.
* Filter by size (whale-only mode).
* Click a row → tx hash on the explorer.

### Mobile gestures

The chart is full-width on mobile with full gesture support:

* **Pinch** to zoom in/out.
* **Drag** to pan horizontally.
* **Long-press** for hover info.
* **Double-tap** to reset zoom.

### Developer API

Chart data from the Indexer endpoint:

```
GET /api/markets/:id/candles?timeframe=1h&from=...&to=...
→ [
  { ts, open, high, low, close, volume },
  ...
]
```

Or price snapshots only:

```
GET /api/markets/:id/price-history?from=...&to=...
→ [{ ts, yesPrice, source }]
```

Details: [Indexer API](../../developers-guide/api-reference.md#indexer-endpoints).

### Reading prediction market charts

{% hint style="info" %}
### Tips for reading prediction market charts

* **Volume spike after endTime** — unusual activity, likely arbitrage as resolution approaches.
* **Price pinned at $0.50** — the market lacks conviction; information is unclear.
* **Sharp move** — new information has hit; check the news.
* **Price near $0.95-$0.99** — approaching YES resolution. Low risk-reward (gain only 5%, risk losing 95%).
* **Price near $0.01-$0.05** — tail event; high risk-reward but low probability.
{% endhint %}

{% hint style="warning" %}
**Do not confuse a prediction market chart with a cryptocurrency chart.** The Y-axis is a **probability bounded in 0-1**, not an unbounded asset price. The same chart shape means different things.
{% endhint %}
