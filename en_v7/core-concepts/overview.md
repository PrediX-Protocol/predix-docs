---
description: PrediX combines a Central Limit Order Book with Uniswap v4.
---

# PrediX Market

### About PrediX

Trading on PrediX combines two liquidity sources in a single trade - the **Central Limit Order Book (CLOB)** where makers set prices, and the **AMM pool** (Uniswap v4) that always quotes a price. The Router automatically picks the best route per trade, so you get the best price available across both, on every order.

This is familiar for both traders and market makers. **Market orders** execute instantly at the best available price across CLOB and AMM. **Limit orders** let you set your price, participate in price discovery, and rest on the orderbook until matched.

***

### Market Types on PrediX

| Kind                    | Description                                      | Example                                                           |
| ----------------------- | ------------------------------------------------ | ----------------------------------------------------------------- |
| **Binary**              | Simple YES / NO                                  | "BTC > $100k before 2027?"                                        |
| **Scalar**              | Long / short with a strike, linear payout        | "Vietnam GDP 2026 (USD billion)?" — long > strike, short < strike |
| **Multi-outcome event** | N sub-markets mutually exclusive, exactly 1 wins | "Who wins FIFA WC 2026?" - 48 teams, 1 market per team            |
| **Sports**              | Pre-structured for tournaments                   | Premier League season winner                                      |
| **Grouped**             | Markets grouped by theme                         | "AI capabilities milestones 2026" group                           |

{% hint style="info" %}
**Multi-outcome details:** [**Multi-outcome events**](../users-guide/multi-outcome-markets.md)**.**
{% endhint %}

***

### The Orderbook (CLOB)

Each market has a **YES** and **NO** orderbook, with separate **Bids** (buy orders) and **Asks** (sell orders).

For example, in a market about the BTC price:

| Side         | Price   | Meaning                                                                                  |
| ------------ | ------- | ---------------------------------------------------------------------------------------- |
| Best **Ask** | `$0.51` | Lowest price someone is willing to sell YES at - you pay this if you Buy at market       |
| Best **Bid** | `$0.49` | Highest price someone is willing to pay for YES - you receive this if you Sell at market |
| **Spread**   | `$0.02` | Difference between bid and ask - tighter spread = better liquidity                       |

The lower the spread, the more efficient the market. As makers place tighter limit orders, the spread shrinks and execution improves for everyone.

{% hint style="info" %}
**Why prices are between $0 and $1?**

* A YES share pays exactly `$1` if the event happens, and `$0` if it doesn't. So the market price is the **probability** the event happens. `$0.51` = 51% chance.
{% endhint %}

***

### The AMM Pool

Behind every market is also a **Uniswap v4 pool** with a custom hook designed for prediction markets. The pool always has liquidity, even when the orderbook is thin or empty.

| Property             | Value                                                            |
| -------------------- | ---------------------------------------------------------------- |
| **Pricing**          | Continuous - every price between `$0.01` and `$0.99` is quotable |
| **Liquidity source** | LP positions (anyone can be an LP)                               |
| **Fee**              | `0.30%` per swap, paid to LPs                                    |
| **Availability**     | 24/7, even on brand-new markets with empty orderbooks            |

The AMM ensures you can always trade, no matter the orderbook depth. This is PrediX's edge over pure orderbook exchanges where new markets often start with no liquidity.

***

### The Router - Best of Both

When you place an order, the **Router** queries both CLOB and AMM, then routes your trade to the best price available via [CLOB + AMM Hybrid](clob-amm-hybrid.md).

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

You don't choose CLOB vs AMM - just click **Buy** or **Sell**. The Router handles routing automatically and delivers the best execution.
