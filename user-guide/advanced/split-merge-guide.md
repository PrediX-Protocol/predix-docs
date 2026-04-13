---
description: Advanced feature — create and recombine tokens
---

# Split & Merge (Advanced)

> This is an advanced feature. Most traders don't need it.

## What is Split?

**Split** converts USDC into equal amounts of YES and NO tokens:

```
Split $100 USDC → 100 YES tokens + 100 NO tokens
```

## What is Merge?

**Merge** is the reverse — combine YES and NO back into USDC:

```
100 YES tokens + 100 NO tokens → $100 USDC
```

## Why Would I Use This?

### Reason 1: Arbitrage

If YES = $0.40 and NO = $0.45 (total $0.85):

```
Split $1 → 1 YES + 1 NO (cost: $1)
Sell YES for $0.40 + Sell NO for $0.45 = $0.85
Loss: $0.15 ← not profitable this way

BUT if YES = $0.55 and NO = $0.55 (total $1.10):
Buy 1 YES ($0.55) + Buy 1 NO ($0.55) = $1.10
Merge → $1.00
Loss: $0.10 ← not profitable either

Arbitrage only works when total ≠ $1.00
```

### Reason 2: Get Both Tokens

If you want to be a market maker and sell both sides:

```
Split $500 → 500 YES + 500 NO
Place SELL YES orders at $0.55
Place SELL NO orders at $0.55
Earn from both sides
```

### Reason 3: Hedge

If you already hold YES and want to reduce risk:

```
You hold 100 YES (bought at $0.40)
Split $50 → 50 YES + 50 NO
Now: 150 YES + 50 NO
If NO wins: you get $50 from NO (partial hedge)
```

## How to Split

1. Go to a market page
2. Find **"Split / Merge"** section
3. Click **"Split"** tab
4. Enter USDC amount
5. Click **"Split"**
6. Confirm in wallet

## How to Merge

1. **"Split / Merge"** section
2. Click **"Merge"** tab
3. Enter amount (must have equal YES and NO)
4. Click **"Merge"**
5. USDC returned to wallet

> ⚠️ You need **equal** amounts of YES and NO to merge. If you have 100 YES but only 50 NO, you can only merge 50.

---

**Next**: [Buy & Sell](../trading/buy-sell.md) · [Providing Liquidity](../../liquidity/overview.md)
