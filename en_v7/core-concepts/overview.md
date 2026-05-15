---
description: >-
  PrediX combines a Central Limit Order Book with Uniswap v4. Its Smart Router
  automatically executes every trade at the best available price, ensuring 24/7
  liquidity and optimal execution.
---

# Overview

Trading on PrediX combines two liquidity sources in a single trade — the **Central Limit Order Book (CLOB)** where makers set prices, and the **AMM pool** (Uniswap v4) that always quotes a price. The Router automatically picks the best route per trade, so you get the best price available across both, on every order.

This is familiar for both traders and market makers. **Market orders** execute instantly at the best available price across CLOB and AMM. **Limit orders** let you set your price, participate in price discovery, and rest on the orderbook until matched.

### The Orderbook (CLOB)

Each market has a **YES** and **NO** orderbook, with separate **Bids** (buy orders) and **Asks** (sell orders).

For example, in a market about the BTC price:

| Side         | Price   | Meaning                                                                                  |
| ------------ | ------- | ---------------------------------------------------------------------------------------- |
| Best **Ask** | `$0.51` | Lowest price someone is willing to sell YES at — you pay this if you Buy at market       |
| Best **Bid** | `$0.49` | Highest price someone is willing to pay for YES — you receive this if you Sell at market |
| **Spread**   | `$0.02` | Difference between bid and ask — tighter spread = better liquidity                       |

The lower the spread, the more efficient the market. As makers place tighter limit orders, the spread shrinks and execution improves for everyone.

{% hint style="info" %}
**Why prices are between $0 and $1.** A YES share pays exactly `$1` if the event happens, and `$0` if it doesn't. So the market price is the **probability** the event happens. `$0.51` = 51% chance.
{% endhint %}

### The AMM Pool

Behind every market is also a **Uniswap v4 pool** with a custom hook designed for prediction markets. The pool always has liquidity, even when the orderbook is thin or empty.

| Property             | Value                                                            |
| -------------------- | ---------------------------------------------------------------- |
| **Pricing**          | Continuous — every price between `$0.01` and `$0.99` is quotable |
| **Liquidity source** | LP positions (anyone can be an LP)                               |
| **Fee**              | `0.30%` per swap, paid to LPs                                    |
| **Availability**     | 24/7, even on brand-new markets with empty orderbooks            |

The AMM ensures you can always trade, no matter the orderbook depth. This is PrediX's edge over pure orderbook exchanges where new markets often start with no liquidity.

### The Router — Best of Both

When you place an order, the **Router** queries both CLOB and AMM, then routes your trade to the best price available.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

You don't choose CLOB vs AMM — just click **Buy** or **Sell**. The Router handles routing automatically and delivers the best execution.

### Order Types

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th></th><th></th></tr></thead><tbody><tr><td><mark style="color:orange;"><strong>Market Order</strong></mark></td><td>Instant execution at the best available price across CLOB + AMM</td><td>Best For<strong>:</strong> Quick trades, small size, accepting slippage</td></tr><tr><td><mark style="color:orange;"><strong>Limit Order</strong></mark></td><td>Set your own price and rest on the orderbook until matched</td><td>Best For: Price control, large size, earning maker rebates</td></tr></tbody></table>

### Advanced Features

<table data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><mark style="color:orange;"><strong>Split &#x26; Merge</strong></mark></td><td>Convert between USDC collateral and YES+NO share pairs</td></tr><tr><td><mark style="color:orange;"><strong>Managing Orders</strong></mark></td><td>Cancel, edit, and track your open orders</td></tr><tr><td><mark style="color:orange;"><strong>Multi-Outcome Markets</strong></mark></td><td>Trade markets with 3+ possible outcomes</td></tr></tbody></table>

***
