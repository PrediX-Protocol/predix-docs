---
description: Understand how to read the prediction market chart on PrediX.
icon: chart-line-up-down
---

# Chart & Timeframe

The chart on PrediX may look similar to a conventional trading chart, but it functions differently. This page is designed to help you understand its components

### Understanding the chart

#### <mark style="color:orange;">Price = Probability</mark>

PrediX charts are **not** crypto price charts. Prices are shown in **cents (¢)** and represent the **probability of the event happening**.

> Example: YES at `63¢` means the market prices a \~63% chance of the event occurring. _(Illustrative only — every market differs.)_

Since `YES + NO = 100¢`, looking at one side tells you the other.

**Trading range: 1¢ – 99¢** (1¢ steps). There is no `0¢` or `100¢` while trading is open — these only appear after the market resolves: winning side = `100¢` ($1), losing side = `0¢`.

| Price zone | What it means                |
| ---------- | ---------------------------- |
| Near 50¢   | Both sides roughly tied      |
| Near 99¢   | Almost certain to happen     |
| Near 1¢    | Almost certain not to happen |

### Market header

* **Yes / No** current prices
* **Volume** total traded
* **Trader count**
* **Countdown** to endTime

### Two chart modes

<table data-view="cards"><thead><tr><th></th><th></th><th data-hidden data-card-cover data-type="image">Cover image</th></tr></thead><tbody><tr><td><mark style="color:orange;"><strong>Binary chart</strong></mark></td><td>for YES/NO markets. Shows a single price line (YES by default). The right-side axis updates with the live match price.</td><td></td></tr><tr><td><mark style="color:orange;"><strong>Multi-outcome chart</strong></mark></td><td>for markets with 3+ choices (elections, tournaments...). Plots multiple lines simultaneously, one color per choice, with a legend on top.</td><td></td></tr></tbody></table>

* A line breaking away upward = the leading choice
* Lines clustered together = choices running neck and neck

Below the chart, each choice has its own row showing name, individual volume, **implied probability %**, and quick **Yes / No** buttons. See Multi-Outcome Markets.

{% hint style="info" %}
**Powered by TradingView** — charts are built on the TradingView library (look for the **TV** logo in the lower corner). Drag, zoom, and hover for OHLC details like on professional trading platforms.
{% endhint %}

***

### Timeframe

| Range        | Best for                           | Strategy         |
| ------------ | ---------------------------------- | ---------------- |
| `5m` · `15m` | Intraday volatility, breaking news | Scalping         |
| `1H` · `4H`  | Hours to a few days                | Trend trading    |
| `1D`         | Multi-day holds                    | Mid-term         |
| `1W` · `ALL` | Months out, macro view             | Position trading |

Not sure which to pick? Leave it on default — the app chooses based on time remaining in the market.

### Reading the chart

* **Candle / line toggle:** top-right corner button.
* **Y-axis auto-scales** to the data range — not fixed to 0–100.
* **Volume bars** at the bottom: tall bars = heavy trading at that moment.

**Common signals**

* Price stuck near the middle → market lacks conviction
* Sudden sharp move → fresh news has hit
* Heavy action near endTime → traders closing positions

### Order Book

* **YES / NO tabs:** choose which side's book to view
* **0.01 / 0.1:** price grouping
* **ASK (sell) / BID (buy):** each level shows quantity + cumulative total
* **Spread:** tighter = better liquidity

> The **Buy / Sell** prices at the top of the Order Book are the **effective prices** you'll actually get — already blended from the order book and the liquidity pool. Even when the book looks empty, you still get a fair price thanks to the CLOB + AMM Hybrid model.

### Recent Trades

Real-time list of executed trades: time, wallet (truncated), buy/sell, size, YES/NO side, fill price, and value. Click a row to open the transaction on the explorer.

A quick way to feel whether the market is hot or quiet, and which side whales are taking.

***

> Chart prices are **probabilities (1¢–99¢)**, not asset prices. The shape may look familiar from crypto charts, but it means something different.
