---
description: Understanding prediction markets and how prices reflect probability
---

# Prediction Markets

## What is a Prediction Market?

A prediction market is a marketplace where participants trade on the outcome of future events. Prices of outcome tokens reflect the collective probability assessment of the crowd.

## How Prices Reflect Probability

Each market creates two tokens:

- **YES token**: Pays $1.00 if the event occurs
- **NO token**: Pays $1.00 if the event does NOT occur

If YES trades at $0.70, the market implies a **70% probability** that the event will happen.

## The Price Anchor: YES + NO ≈ $1.00

Because anyone can:

- **Split** 1 USDC into 1 YES + 1 NO
- **Merge** 1 YES + 1 NO back into 1 USDC

Arbitrageurs enforce:

```
YES price + NO price ≈ $1.00

If YES + NO > $1.00 → split + sell both → profit (pushes prices down)
If YES + NO < $1.00 → buy both + merge → profit (pushes prices up)
```

This is the fundamental invariant of all prediction markets.

## History

| Platform | Model | Status |
| -------- | ----- | ------ |
| Polymarket | Centralized CLOB on Polygon | Largest by volume, ERC-1155 tokens |
| Augur | Decentralized AMM on Ethereum | Pioneer, limited adoption |
| Kalshi | Centralized exchange (CFTC regulated) | Traditional finance approach |
| Omen (Gnosis) | AMM-based on Gnosis Chain | Community-driven |
| **PrediX** | **Hybrid CLOB+AMM on Uniswap v4** | **ERC-20 tokens, Diamond proxy** |

---

**Next**: [Outcome Tokens](outcome-tokens.md) · [Split & Merge](split-and-merge.md) · [How It Works](../getting-started/how-it-works.md)
