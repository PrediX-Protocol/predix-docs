---
description: Read prediction market charts, change timeframes, and apply indicators.
icon: chart-line-up-down
---

# Chart & Timeframe

Prediction market charts are not crypto charts. The Y-axis is bounded between $0 and $1 - a probability, not an unbounded asset price. PrediX renders prediction market data accurately while preserving the tooling traders expect from professional interfaces.

### Understanding the chart

The chart aggregates prices from three on-chain sources into a single canonical YES price / time series, All three feed into a unified combined chart on the _**Market detail page.**_

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="image">Cover image</th></tr></thead><tbody><tr><td><mark style="color:orange;"><strong>Hook AMM swaps</strong></mark></td><td>Tick-by-tick AMM prices for analytics.</td><td><a href="../../.gitbook/assets/44.jpg">44.jpg</a></td></tr><tr><td><mark style="color:orange;"><strong>CLOB matches</strong></mark></td><td>Price at each limit fill.</td><td><a href="../../.gitbook/assets/43.jpg">43.jpg</a></td></tr></tbody></table>

***

#### <mark style="color:$warning;">1. Reading OHLC candles</mark>

Each candle shows the **open / high / low / close** for that period. Volume is displayed as bars at the bottom of the chart.

```
─┬─ high
 │
 │  close (green if close > open)
 ┃
 │  open
 │
─┴─ low
```

#### <mark style="color:$warning;">2. Timeframes</mark>

PrediX supports six timeframes, each suited to a different style of trading.

| Timeframe                                   | Use case                                             |
| ------------------------------------------- | ---------------------------------------------------- |
| <ul><li><strong>1m</strong></li></ul>       | <ul><li>Scalp, intraday</li></ul>                    |
| <ul><li><strong>5m</strong></li></ul>       | <ul><li>Short-term momentum</li></ul>                |
| <ul><li><strong>15m - 1h</strong></li></ul> | <ul><li>Day trade</li></ul>                          |
| <ul><li><strong>4h</strong></li></ul>       | <ul><li>Swing</li></ul>                              |
| <ul><li><strong>1D</strong></li></ul>       | <ul><li>Position trade, multi-day hold</li></ul>     |
| <ul><li><strong>1W</strong></li></ul>       | <ul><li>Long market (3+ months to endTime)</li></ul> |

The app picks a sensible default based on time-to-end:

* [x] < 24h to endTime → 5m
* [x] 1-7 days → 15m or 1h
* [x] 7 days → 1h or 4h
* [x] 30 days → 1D

#### <mark style="color:$warning;">3. Compare two markets</mark>

Click **Compare** then select another market. The chart overlays two YES price lines on the same axis (normalized 0-1, since both are already probabilities).

Useful pairings include "Trump win" vs "Biden win" during the 2024 election cycle.

#### <mark style="color:$warning;">4. Order book depth</mark>

The **Depth** tab next to the chart shows:

* Bids (BUY orders) on the left, asks (SELL orders) on the right.
* Cumulative volume as walls of liquidity.

Useful for spotting a **liquidity wall** - a large limit order that may stall the price.

#### <mark style="color:$warning;">5. Recent trades</mark>

The **Trades** tab lists every trade in real time:

* Side, size, price, timestamp.
* Filter by size (whale-only mode).
* Click a row → tx hash on the explorer.

#### <mark style="color:$warning;">6. Mobile gestures</mark>

The chart is full-width on mobile with full gesture support:

* **Pinch** to zoom in/out.
* **Drag** to pan horizontally.
* **Long-press** for hover info.
* **Double-tap** to reset zoom.

***

### Developer API

{% hint style="info" %}
Details: [API Reference](/broken/pages/NaTE7epVHDvl0K9Pol1r).
{% endhint %}

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

***

{% hint style="success" %}
### Tips for reading prediction market charts

* **Volume spike after endTime** - unusual activity, likely arbitrage as resolution approaches.
* **Price pinned at $0.50** - the market lacks conviction; information is unclear.
* **Sharp move** - new information has hit; check the news.
* **Price near $0.95-$0.99** - approaching YES resolution. Low risk-reward (gain only 5%, risk losing 95%).
* **Price near $0.01-$0.05** - tail event; high risk-reward but low probability.
{% endhint %}

{% hint style="warning" %}
**Do not confuse a prediction market chart with a cryptocurrency chart.** The Y-axis is a **probability bounded in 0-1**, not an unbounded asset price. The same chart shape means different things.
{% endhint %}
