---
description: What happens when a market ends
---

# What Happens When a Market Ends?

## Timeline

```
1. Market Created     → Trading opens
2. Trading Period     → Buy/sell YES and NO shares
3. Market Expires     → Trading stops (endTime reached)
4. Resolution         → Oracle determines outcome (YES or NO)
5. Redemption         → Winners claim $1.00 per winning share
```

## Step by Step

### 1. Market Expires

When the countdown reaches zero, **no more trading**. You can't buy or sell new shares.

### 2. Oracle Resolves

An **oracle** determines the real-world outcome:

- **Price markets** (e.g., "BTC > $100K?"): Chainlink price feed checks automatically
- **Event markets** (e.g., "Who wins election?"): Admin verifies and submits result
- Usually happens within minutes to hours after expiry

### 3. Redeem Your Winnings

If you hold **winning** shares:

```
Click "Redeem" → Your winning shares are burned → USDC sent to your wallet

100 winning shares → $100 USDC
```

If you hold **losing** shares:

```
Your shares are worth $0. No action needed.
They simply have no value.
```

## What If I Want to Exit Early?

You **don't have to wait** for resolution. You can sell your shares anytime before the market expires.

```
Bought YES at $0.30 → Price is now $0.60
→ Sell now for $0.60 → Take profit immediately
→ No need to wait for resolution
```

## What If the Oracle is Wrong?

PrediX has safety mechanisms:

| Mechanism | What it does |
|-----------|-------------|
| **Emergency Resolve** | Admin can override after 7 days |
| **Refund Mode** | Everyone gets proportional refund if market is invalid |

> 💡 Markets are designed to resolve correctly. Emergency mechanisms are a safety net, rarely used.

## Multi-Outcome Markets

For markets with multiple outcomes (e.g., "Who wins the election?"):

```
5 candidates: Trump, DeSantis, Newsom, Harris, Other
→ Admin selects the winner (e.g., index 0 = Trump)
→ Trump YES shares = $1.00
→ All other YES shares = $0.00
→ All other NO shares = $1.00 (because those candidates didn't win)
```

---

**Next**: [Risks & Tips](risks-and-tips.md) · [Your First Trade](../user-guide/getting-started/first-trade.md)
