---
description: Step-by-step flow from market creation to token redemption
---

# How It Works

## Market Lifecycle

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│ Created  │ →  │  Active  │ →  │ Expired  │ →  │ Resolved │ →  │Redeemable│
└──────────┘    └──────────┘    └──────────┘    └──────────┘    └──────────┘
                     │                                │
                  Trading                        Emergency (7d)
                  allowed                        or Refund Mode
```

## Step by Step

### 1. Create Market

An admin creates a market with a question, end time, and oracle:

```
"Will BTC exceed $100,000 by December 31, 2026?"
  → YES token + NO token + Uniswap v4 AMM pool (auto-initialized at $0.50)
```

### 2. Trade

Users acquire outcome tokens by trading:

- **Split**: Deposit 1 USDC → receive 1 YES + 1 NO token
- **Buy via Router**: Pay USDC → Smart Router finds best price across CLOB + AMM → receive YES or NO tokens
- **Limit Order**: Place orders on the CLOB at a specific price

### 3. Price Discovery

Token prices reflect the market's view of probability:

| YES price | Interpretation |
| --------- | -------------- |
| $0.70 | Market believes 70% chance of YES outcome |
| $0.30 | Market believes 30% chance of YES outcome |

> ⚠️ **Key invariant**: YES price + NO price ≈ $1.00 (enforced by split/merge arbitrage)

### 4. Resolve

After the event occurs:

1. The oracle (Manual or Chainlink) reports the outcome
2. Anyone calls `resolveMarket(marketId)` to finalize

### 5. Redeem

Winning token holders redeem for $1.00 per token:

```
If YES wins:
  1 YES token → $1.00 USDC
  1 NO token  → $0.00

If NO wins:
  1 YES token → $0.00
  1 NO token  → $1.00 USDC
```

## Worked Example

Alice thinks BTC will exceed $100K. Current YES price: $0.65.

| Action | Alice pays | Alice receives | P&L |
| ------ | ---------- | -------------- | --- |
| Buy 100 YES @ $0.65 | 65 USDC | 100 YES | — |
| BTC exceeds $100K ✅ | — | — | — |
| Redeem 100 YES | — | 100 USDC | **+35 USDC (54% return)** |

If BTC does NOT exceed $100K, Alice's 100 YES tokens are worth $0. Loss: 65 USDC.

---

**Next**: [Quick Start](quick-start.md) · [Outcome Tokens](../concepts/outcome-tokens.md) · [Split & Merge](../concepts/split-and-merge.md)
