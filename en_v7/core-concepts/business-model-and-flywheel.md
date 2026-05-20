---
description: Long-term economic architecture is structured as a four-layer strategic model.
---

# Business Model & Flywheel

PrediX's long-term economic architecture is structured as a four-layer strategic model, where each layer reinforces the next to create a self-sustaining cycle of liquidity, usage, and value capture.

The protocol is not designed around a single revenue stream. It is engineered as a coordinated system in which execution efficiency generates fees, token mechanics translate those fees into deflationary pressure and aligned incentives, growth primitives continuously expand the user base, and financial products retain capital inside the ecosystem.

***

### Layer 1 - Core Revenue Engine

The foundational layer of PrediX captures fees from trading activity through an architecture purpose-built for prediction markets.

#### <mark style="color:$warning;">**1. Hybrid CLOB + AMM Execution**</mark>

The Smart Router prioritizes execution against on-chain CLOB liquidity to achieve optimal pricing, and automatically routes remaining order flow through Uniswap v4 pools to access deeper passive liquidity. The entire process settles atomically within a single transaction.

Revenue is captured from both venues:

* AMM dynamic fee - between 0.5% and 5% depending on time to expiry
* CLOB spread capture - through synthetic mint and merge matching, where the protocol earns the spread when bid + ask exceeds or falls short of $1

#### <mark style="color:$warning;">2. Single Pool YES/USDC</mark>

Each market operates with a single liquidity pool. Because outcome tokens satisfy the invariant YES + NO = 1 USDC, the protocol does not require a separate NO/USDC pool - all liquidity consolidates into a unified venue.

This architecture delivers materially higher capital efficiency than the fragmented orderbook design used by traditional prediction market platforms, allowing long-tail markets to remain executable with minimal liquidity bootstrap.

#### <mark style="color:$warning;">**3. Dynamic Fee LP Protection**</mark>

Trading fees on the AMM scale upward as a market approaches resolution:

| Time to Expiry | Dynamic Fee |
| -------------- | ----------- |
| 7+ days        | 0.5%        |
| 3–7 days       | 1%          |
| 1–3 days       | 2%          |
| Under 24 hours | 5%          |

This mechanism compensates liquidity providers for the elevated informational asymmetry that emerges as markets near settlement, protecting LPs from toxic flow and preserving long-term liquidity sustainability.

#### <mark style="color:$warning;">**4. ERC-20 Composable Outcome Assets**</mark>

YES and NO outcome tokens are issued as standard ERC-20 assets, enabling direct integration with the broader DeFi ecosystem. Outcome tokens can be used as collateral in lending markets, deployed into yield strategies, paired in Uniswap pools, or composed into structured products.

This transforms prediction positions from isolated speculative instruments into programmable financial primitives.

***

### Layer 2 - Token Flywheel

The PRX token coordinates liquidity, governance, and value capture through a self-reinforcing economic loop inspired by the Curve model and adapted for prediction markets.

{% stepper %}
{% step %}
<mark style="color:$warning;">**Step 1: Lock PRX for vePRX**</mark>

* Users acquire PRX and lock their tokens to receive vePRX voting power, aligning long-term participation with governance influence and ecosystem incentives.
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 2: Direct Gauge Voting**</mark>

* vePRX holders allocate gauge votes toward specific prediction market pools, determining where liquidity incentives and treasury emissions are distributed.
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 3: Boost LP Incentives**</mark>

* Pools receiving higher vote allocation gain additional LP subsidies from the treasury, increasing total APR and improving liquidity provider participation.
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 4: Deepen Market Liquidity**</mark>

* Higher LP yields attract deeper liquidity across supported markets, reducing slippage and improving execution efficiency for traders.
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 5: Increase Trading Volume**</mark>

* Improved execution quality encourages higher trading activity, generating greater protocol fee revenue through both AMM and CLOB infrastructure.
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 6: Route Revenue Back to PRX**</mark>

Protocol revenue is recycled into the ecosystem through:

* USDC real yield distribution to PRX stakers
* Buyback-and-burn mechanisms that reduce circulating supply

This reinforces long-term value accrual for PRX holders.
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 7: Compound the Flywheel**</mark>

As protocol revenue and token utility increase, demand for PRX grows, more tokens become locked, and the liquidity flywheel compounds over time.
{% endstep %}
{% endstepper %}

Each rotation of the flywheel deepens liquidity, strengthens the token's value capture mechanism, and increases the protocol's competitive moat.

***

### Layer 3 - Growth & Acquisition Layer

PrediX's growth strategy is engineered to minimize customer acquisition cost by embedding viral and community-driven primitives directly into the product.

#### <mark style="color:$warning;">**1. Crowdfunded Market Creation**</mark>

The community itself funds the launch of new markets. Users propose markets through the Market Kickstarter, backers commit USDC during a 7-day window, and when the target is reached the smart contract automatically deploys the market and seeds the initial liquidity pool.

Initial backers receive NFT revenue-share tokens representing a permanent claim on a percentage of LP fees from that market, aligning long-term incentives with market performance.

#### <mark style="color:$warning;">**2. Group Buying**</mark>

A native social-trading primitive that transforms acquisition into a viral loop. Users create Team Buy Links for specific markets, share them across their networks, and when a threshold of participants is reached the entire group receives improved execution pricing or fee discounts of 2–5%.

This mechanism is particularly effective for KOLs and streamers, who can mobilize their communities around prediction events the same way they coordinate raids or group purchases - turning each user into a distribution channel.

#### <mark style="color:$warning;">**3. Points & Seasons System**</mark>

A six-season incentive program (S0–S6) distributing 350M PRX over three years, designed to bootstrap early adoption and reward sustained engagement.

* Season weights decline approximately 30% per season, front-loading rewards toward the most critical bootstrap phase.
* Season 1 launches with a zero-fee trading window to accelerate volume buildup at mainnet.
* Major global events (such as the FIFA World Cup) trigger multiplier boosts of 2.5×–3×, capitalizing on natural attention spikes to convert mainstream audiences into prediction market users.

***

### Layer 4 - Retention & Financial Products Layer

Once users are inside the ecosystem, an expanding suite of financial products is designed to retain capital, increase trading volume, and broaden the addressable market beyond crypto-native traders.

#### <mark style="color:$warning;">**1. Perp Prediction**</mark>

Scheduled for Phase 3 (Season 4, months 19–24), Perpetual Prediction Markets eliminate the concept of expiry entirely. Positions remain open indefinitely, with prices balanced through a continuous funding rate computed on every block.

This product targets professional traders, market makers, and high-frequency strategies that require sustained directional exposure. Critically, PRX is the required margin asset, locking deep institutional capital into the token economy and creating durable structural demand.

#### <mark style="color:$warning;">**2. BNPL - Buy Now, Pay Later**</mark>

A consumer credit primitive built natively for prediction markets, allowing users - particularly retail in Southeast Asia - to open positions larger than their current balance and repay in fixed installments.

* Positions are fully collateralized by the locked outcome tokens themselves.
* No funding rates, no margin calls - only a fixed, predictable repayment schedule modeled on familiar consumer finance products such as Klarna, Atome, and Kredivo.
* Average position size increases, driving higher protocol volume and fee generation.

#### <mark style="color:$warning;">**3. Lossless Lottery**</mark>

A capital-preserving deposit product that onboards mainstream retail users who are uncomfortable with traditional speculation. Users deposit USDC into the Lossless Pool, the protocol deploys the capital into high-probability YES tokens (>95%) to generate a safe yield spread, and the aggregated yield is distributed via random jackpot drawings using Chainlink VRF.

* User principal remains intact and withdrawable.
* Yield is reallocated as winner-takes-most prizes, creating lottery-like excitement without lottery-like risk.
* Idle USDC stays inside the ecosystem, contributing to protocol TVL and treasury yield.

#### <mark style="color:$warning;">**4. Liquidity Manager**</mark>

An in-app interface that turns AMM liquidity provision into a retail-grade product. Users can add, remove, and monitor LP positions without leaving PrediX or interacting directly with Uniswap.

The dashboard surfaces seven key metrics in real time - fee earned, current impermanent loss, net P\&L, break-even days, pool share, days to expiry, and fee APR - alongside automated alerts at critical thresholds (IL > 15%/25%, probability > 75% or < 25%, less than 24 hours to expiry).

By removing the operational complexity of DeFi liquidity management, the Liquidity Manager retains a wider base of LPs and stabilizes long-term pool depth.

***

### System Summary

| **Layer**                                                                                                                                  | **Primary Function**                           | **Outcome**                       |
| ------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------- | --------------------------------- |
| [#layer-1-core-revenue-engine](business-model-and-flywheel.md#layer-1-core-revenue-engine "mention")                                       | Capture fees from CLOB + AMM trading           | Sustainable protocol revenue      |
| [#layer-2-token-flywheel](business-model-and-flywheel.md#layer-2-token-flywheel "mention")                                                 | Coordinate liquidity and value capture via PRX | Compounding demand for the token  |
| [#layer-3-growth-and-acquisition-layer](business-model-and-flywheel.md#layer-3-growth-and-acquisition-layer "mention")                     | Viral and community-driven user acquisition    | Low CAC, organic market expansion |
| [#layer-4-retention-and-financial-products-layer](business-model-and-flywheel.md#layer-4-retention-and-financial-products-layer "mention") | Retain capital, expand addressable market      | Higher TVL, recurring volume      |

Each layer feeds the others. Trading generates fees, fees drive PRX value capture, PRX coordinates deeper liquidity, deeper liquidity attracts more users, growth primitives expand the user base, and financial products retain the capital they bring with them.

> **This is the operational foundation of PrediX - not a single product, but a compounding financial system for predictive markets.**
