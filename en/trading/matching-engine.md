---
description: Three-phase matching algorithm — COMPLEMENTARY, MINT, and MERGE
---

# Matching Engine

The PrediXExchange uses a 99-tick bitmap for gas-efficient order scanning across three matching types.

## Match Types

### COMPLEMENTARY — Direct Swap

Two opposite orders for the same token:

```
BUY_YES @ $0.60  ↔  SELL_YES @ $0.50
→ Match at maker price ($0.50)
→ Buyer gets YES, seller gets USDC
→ Buyer receives $0.10 price improvement (refund)
```

### MINT — Both Sides Buying (sum ≥ $1.00)

```
BUY_YES @ $0.60  +  BUY_NO @ $0.50  =  $1.10 ≥ $1.00
→ Split USDC → YES + NO
→ YES delivered to YES buyer, NO delivered to NO buyer
→ Surplus $0.10 → protocol fee
```

### MERGE — Both Sides Selling (sum ≤ $1.00)

```
SELL_YES @ $0.40  +  SELL_NO @ $0.50  =  $0.90 ≤ $1.00
→ Merge YES + NO → USDC
→ USDC split between sellers by price
→ Surplus $0.10 → protocol fee
```

## MatchType Enum

```solidity
enum MatchType {
    COMPLEMENTARY, // 0
    MINT,          // 1
    MERGE          // 2
}
```

## Matching Priority

When a new order is placed, the engine attempts matching in this order:

1. **COMPLEMENTARY** — check for direct counterparty orders
2. **MINT** — check if opposite-side buy orders can pair
3. **MERGE** — check if opposite-side sell orders can pair
4. **Rest** — unfilled amount becomes a resting order on the book

---

**Next**: [Order Book](order-book.md) · [Virtual NO Pricing](virtual-no-pricing.md)
