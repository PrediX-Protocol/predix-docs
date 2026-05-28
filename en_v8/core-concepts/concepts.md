---
description: Understand what is Prediction Market and how PrediX works.
---

# Prediction Context

A market for trading tokens that reflect the **probability** of a future event occurring. Prices update in realtime based on supply and demand.

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

### dExample

```
Event: "Bitcoin above $100,000 before 2027-01-01?"
Current market pricing:
YES = $0.68 -> the market estimates a 68% probability the event will happen
NO = $0.32 -> 32% probability it won't happen
Total YES + NO ≈ $1.00
```

{% hint style="info" %}
**Why total YES + NO ≈ `$1.00`?**

* See [Outcome\_Tokens](outcome-tokens.md)
{% endhint %}

Payout matrix when the market resolves:

| Actual outcome           | YES holder        | NO holder        |
| ------------------------ | ----------------- | ---------------- |
| Event **happens**        | `1 YES = $1.00` ✅ | `1 NO = $0` ❌    |
| Event **doesn't happen** | `1 YES = $0` ❌    | `1 NO = $1.00` ✅ |

***

### Why Price Is a Good Information Signal

* People who believe the event will happen -> buy YES -> push the price up.
* Aggregates opinions from many participants into a single transparent number.
* **Skin in the game**: real money -> traders only profit if they're correct. Stronger than ordinary polls (where respondents have nothing at stake).
* Research consistently shows that prediction markets are often more accurate than expert forecasts for events with statistical data (elections, sports, macroeconomics).

***

### Compared to Traditional Bookmakers

| Category               | Bookmaker (Bet365, 1xBet...)  | Prediction market                      |
| ---------------------- | ----------------------------- | -------------------------------------- |
| Counterparty           | House (the platform)          | Other users (peer-to-peer)             |
| Pricing                | House sets it, wide spread    | Market-driven, AMM + CLOB              |
| Custody                | House holds funds (custodial) | Non-custodial, on-chain                |
| Sell before resolution | Difficult / impossible        | Yes - resell tokens anytime            |
| Auditable              | No                            | Yes - on-chain explorer                |
| Fees                   | Baked into odds (hidden)      | Transparent, shown before confirmation |
| Censorship             | House can ban users           | Permissionless                         |

***

### Limitations of Prediction Markets

<details>

<summary><mark style="color:$primary;"><strong>Oracle Dependence</strong></mark></summary>

* Requires a source to report the outcome on-chain. Oracles can be wrong, disputed, or delayed. PrediX uses pluggable oracles to mitigate single point of failure risk.
* **Read more:** [**Resolution**](resolution.md)**.**

</details>

<details>

<summary><mark style="color:$primary;"><strong>Liquidity Providers</strong></mark></summary>

* Markets with no trading activity -> wide spread -> high slippage. Liquidity providers are incentivized via [gauge voting](../economics/veprx-gauge.md).

</details>

<details>

<summary><mark style="color:$primary;"><strong>Token Value</strong></mark></summary>

* Tokens only have value within their market context
* Example: 1 YES from market A cannot be used in market B. After resolution, losing tokens = $0.

</details>

<details>

<summary><mark style="color:$primary;"><strong>Black Swan Events</strong></mark></summary>

* Extremely rare events may not be priced correctly. Markets self-correct over time as information becomes public.

</details>

***

### Quick Start

Read in order if you're new, or jump to the section you need.

<table data-view="cards"><thead><tr><th></th><th></th></tr></thead><tbody><tr><td><a href="overview.md"><strong>PrediX Market</strong></a></td><td>CLOB with Uniswap v4.</td></tr><tr><td><a href="outcome-tokens.md"><strong>Outcome Tokens</strong></a></td><td>YES/NO, split/merge, $1 invariant.</td></tr><tr><td><a href="clob-amm-hybrid.md"><strong>CLOB + AMM hybrid</strong></a></td><td>combines 2 liquidity mechanisms.</td></tr><tr><td><a href="resolution.md"><strong>Resolution &#x26; Oracle</strong></a></td><td>Who decides the outcome.</td></tr><tr><td><a href="../users-guide/getting-started.md"><strong>Trading Tutorials</strong></a></td><td>Need step-by-step tutorials.</td></tr><tr><td><a href="/broken/pages/KhM4zcVEYvmzF38tqAmc"><strong>Developer Guide</strong></a></td><td>Technical details (smart contracts, events, storage)</td></tr></tbody></table>
