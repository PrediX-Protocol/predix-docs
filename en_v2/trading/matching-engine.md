# Matching Engine

The Exchange CLOB uses a three-phase matching algorithm with three distinct match types.

## Match Types

### COMPLEMENTARY (type 0)

Direct swap between opposite sides.

```
BUY_YES at $0.60 ↔ SELL_YES at $0.55
→ Match at $0.55 (maker price). Buyer pays $0.55, gets 1 YES. Refund: $0.05.
```

### MINT (type 1)

Two buy orders combine. Protocol mints new tokens from USDC.

```
BUY_YES at $0.72 + BUY_NO at $0.30 = $1.02/pair
→ Protocol splits $1.00 USDC → 1 YES + 1 NO
→ YES buyer pays $0.72, NO buyer pays $0.30
→ Surplus $0.02 → protocol revenue
```

Condition: combined price ≥ $1.00

### MERGE (type 2)

Two sell orders combine. Protocol merges tokens back into USDC.

```
SELL_YES at $0.40 + SELL_NO at $0.55 = $0.95/pair
→ Protocol merges 1 YES + 1 NO → $1.00 USDC
→ YES seller receives $0.40, NO seller receives $0.55
→ Surplus $0.05 → protocol revenue
```

Condition: combined price ≤ $1.00

## Price Improvement

Buyers always pay the **maker's price** (or better), never their limit price. If you bid $0.70 and match against a $0.50 ask, you pay $0.50 and receive a $0.20 refund.

## Matching Priority

1. Best price first (highest bid, lowest ask)
2. FIFO within same price level
3. COMPLEMENTARY matches checked before MINT/MERGE

## Next Steps

- [Order Book](order-book.md) — reading depth and spread
- [Limit Orders](limit-orders.md) — placing orders
- [Contract: Exchange](../contracts/exchange.md) — full technical spec
