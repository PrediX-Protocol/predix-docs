---
description: Prediction markets should evolve into composable financial infrastructure.
---

# Core Innovations & Moat

PrediX introduces a modular architecture designed to solve these structural limitations through hybrid liquidity execution, adaptive market infrastructure, composable outcome assets, and consumer-grade accessibility.

{% hint style="danger" %}
**Most existing prediction markets still operate as isolated trading systems with fragmented liquidity, limited composability, weak LP sustainability, and onboarding friction for mainstream users.**
{% endhint %}

![PrediX - Core Innovations & Moat](../.gitbook/assets/protocol-features.png)

***

### **Hybrid CLOB + AMM Routing**

Traditional prediction markets typically rely on either:

* Orderbook-based execution.
* AMM-based liquidity.

**-> Both models introduce tradeoffs.**

Orderbooks provide stronger pricing efficiency but often suffer from fragmented liquidity. AMMs provide deeper passive liquidity but can introduce weaker execution quality under volatile market conditions.

{% hint style="success" %}
PrediX combines both models within a unified execution layer.



**The Smart Router:**

* [x] Prioritizes on-chain CLOB liquidity for optimal pricing,
* [x] Automatically routes remaining liquidity through Uniswap v4 pools,
* [x] Settles execution atomically within a single transaction.



**Users benefit from:**

* [x] Price improvement from orderbook liquidity,
* [x] Deep liquidity fallback from AMM pools,
* [x] seamless execution without manually selecting routing paths.
{% endhint %}

**This architecture enables prediction markets to remain liquid and executable even under fragmented market conditions.**

### **LP Protection & Anti-Toxic Flow**

> **Liquidity providers in prediction markets face structural exposure to informed trading near market resolution.**

As markets approach expiry, traders with informational advantages can disproportionately extract value from passive liquidity providers. This creates one of the largest long-term sustainability problems for prediction market AMMs.

PrediX introduces a custom LP protection framework built on Uniswap v4 Hooks. The protocol dynamically increases trading fees as markets approach resolution:

| TIME TO EXPIRY | DYNAMIC FEE |
| -------------- | ----------- |
| 7+ days        | 0.5%        |
| 3-7 days       | 1%          |
| 1-3 days       | 2%          |
| Under 24 hours | 5%          |

This mechanism is designed to better compensate liquidity providers during periods of elevated informational asymmetry.

<mark style="background-color:yellow;">PrediX also integrates:</mark>

* <mark style="background-color:yellow;">adaptive fee infrastructure,</mark>
* <mark style="background-color:yellow;">custom Uniswap v4 Hooks,</mark>
* <mark style="background-color:yellow;">and anti-sandwich protection using EIP-1153 transient storage</mark>

to reduce toxic flow exposure and improve long-term liquidity sustainability.

### **ERC-20 Composable Outcome Assets**

> **Traditional prediction market positions are typically isolated within closed trading environments.**

PrediX introduces fully composable ERC-20 outcome assets designed to integrate directly with broader DeFi infrastructure.

Outcome assets can be utilized across:

* Uniswap v4 liquidity pools,
* lending protocols,
* collateral systems,
* vault strategies,
* and future structured financial products.

This transforms prediction markets from standalone trading applications into programmable financial infrastructure.

Rather than functioning solely as speculative positions, outcome assets become native DeFi primitives capable of participating across the broader on-chain economy.

### **Permissionless Market Creation & Oracle Multi-Modular**

> **PrediX is designed with a modular oracle architecture that supports multiple market resolution mechanisms.**

Additional oracle systems can be integrated over time without requiring changes to the core market infrastructure.

The protocol currently supports three oracle modes:

* [x] Manual resolution,
* [x] Chainlink Automation (mainnet),
* [x] UMA adapters (mainnet).

This architecture enables prediction markets across multiple objective event categories, including:

* crypto price movements,
* sports results,
* and other externally verifiable outcomes resolved automatically through oracle infrastructure.

{% hint style="info" %}
**In the next phase, PrediX is designed to evolve toward permissionless market creation. Users will be able to create markets by bonding a predefined amount of protocol tokens.**
{% endhint %}

The mechanism is designed with aligned economic incentives:

* <mark style="background-color:yellow;">**Valid markets receive bond refunds and revenue participation,**</mark>
* <mark style="background-color:yellow;">**Malicious or spam markets are subject to slashing penalties.**</mark>

This model enables an open market ecosystem while maintaining market quality and anti-spam protection through economic enforcement.

### **vePRX Gauge Voting**

> PrediX introduces a liquidity coordination model through vePRX gauge voting.

**✅&#x20;**<mark style="color:red;">**Users can:**</mark>

* lock PRX between 1-12 months,
* receive vePRX voting power,
* and direct liquidity incentives toward specific markets.

Longer lock durations increase governance influence and liquidity coordination power.

**✅&#x20;**<mark style="color:red;">**This mechanism is designed to:**</mark>

* reduce circulating supply pressure,
* increase sticky liquidity,
* and create market-level competition for liquidity incentives.

The model applies Curve-style liquidity coordination mechanics to prediction markets, enabling incentives to evolve dynamically around market demand and trading activity.

### **Consumer-Grade Onboarding & Non-Custodial Architecture**

> Prediction markets cannot scale globally without significantly reducing onboarding complexity.

PrediX integrates:

1. <mark style="background-color:yellow;">Passkey-based authentication</mark>
2. <mark style="background-color:yellow;">Smart account infrastructure</mark>
3. <mark style="background-color:yellow;">Sponsored trading transactions</mark>
4. <mark style="background-color:yellow;">Simplified USDC bridging via Circle CCTP V2</mark>

to create a significantly more accessible on-chain trading experience.

{% hint style="success" %}
At the protocol layer, PrediX remains fully non-custodial. This architecture allows users to access predictive markets through a Web2-like onboarding experience while preserving:



* [x] **Self-custody**
* [x] **Transparent settlement**
* [x] **Native on-chain execution.**
{% endhint %}

The system does not custody user funds during execution. The Router operates as a stateless execution layer that processes routing flow and returns assets within the same transaction lifecycle.
