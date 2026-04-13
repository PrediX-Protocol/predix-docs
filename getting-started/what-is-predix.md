---
description: Overview of PrediX Protocol architecture and key differentiators
---

# What is PrediX?

PrediX Protocol is a **decentralized prediction market platform** built on Uniswap v4 and deployed on Unichain L2. It enables anyone to create markets on real-world events and trade outcome tokens in a fully trustless, on-chain environment.

## Key Differentiators

| Feature | PrediX | Others |
| ------- | ------ | ------ |
| **Trading Engine** | Hybrid CLOB + AMM with Smart Router | CLOB only (Polymarket) or AMM only (Augur) |
| **Outcome Tokens** | ERC-20 (composable with all of DeFi) | ERC-1155 (limited composability) |
| **Infrastructure** | Uniswap v4 Hooks + Flash Accounting | Custom or legacy AMM |
| **Upgradeability** | Diamond Proxy (EIP-2535) | Redeploy or no upgrade path |
| **Oracle System** | Pluggable (Manual, Chainlink, extensible) | Single oracle, rigid |
| **LP Protection** | Dynamic fees: 0.5% → 5% near expiry | No protection |

## Architecture Overview

```
                         User
                          │
              ┌───────────┼───────────┐
              ▼           ▼           ▼
         PrediXRouter  PrediXExchange  Diamond Proxy
         (Smart Route)  (CLOB)        (Market Management)
              │           │           │
              │     ┌─────┘     ┌─────┘
              ▼     ▼           ▼
         Uniswap v4        MarketFacet ─── OutcomeTokens (ERC-20)
         PoolManager       AccessControl
              │            OwnableFacet
              ▼            PausableFacet
         PrediXHookV1
         (Dynamic Fees,       Oracle Adapters
          Anti-Sandwich)      ├── ManualOracle
                              ├── ChainlinkAdapter
                              └── [Future: UMA, API3]
```

### Core Contracts

| Contract | Role |
| -------- | ---- |
| **Diamond** | Market management: create, split, merge, resolve, redeem |
| **Exchange** | On-chain CLOB with 99-tick bitmap matching engine |
| **Router** | Smart routing across CLOB + AMM for best execution |
| **Hook** | Uniswap v4 hook: dynamic fees, anti-sandwich, pool registration |
| **Oracle** | Pluggable resolution system (Manual, Chainlink, extensible) |

---

**Next**: [How It Works](how-it-works.md) · [Quick Start](quick-start.md) · [Core Concepts](../concepts/prediction-markets.md)
