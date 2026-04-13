---
description: Categories — markets with more than two outcomes
---

# Multi-Outcome Markets

## Categories

A **category** groups multiple YES/NO markets into one multi-outcome event.

### Example: "Who will win the US Presidential Election 2028?"

```
Category: "US President 2028"
  Market 0: "Trump wins?"     → YES/NO tokens
  Market 1: "DeSantis wins?"  → YES/NO tokens
  Market 2: "Newsom wins?"    → YES/NO tokens
  Market 3: "Harris wins?"    → YES/NO tokens
  Market 4: "Other wins?"     → YES/NO tokens
```

## GroupSplit & GroupMerge

### GroupSplit

Deposit `amount × N` USDC (where N = number of markets in category) to receive `amount` YES + NO per market.

```typescript
// Category has 5 markets. Deposit 500 USDC → 100 YES+NO per market
const amount = ethers.parseUnits("100", 6);
// Requires approval of 100 × 5 = 500 USDC
await usdc.approve(DIAMOND_ADDRESS, ethers.parseUnits("500", 6));
await diamond.groupSplit(categoryId, amount);
```

### GroupMerge

Burn `amount` YES + NO from every market to reclaim `amount × N` USDC.

```typescript
await diamond.groupMerge(categoryId, amount);
// Burns 100 YES + 100 NO from each of 5 markets
// Returns 500 USDC
```

## Resolution

```typescript
// Only 1 winner: market at index 2 wins
await diamond.resolveCategory(categoryId, 2);

// Result:
// Market 2 → YES wins (YES holders redeem $1)
// Markets 0, 1, 3, 4 → NO wins (NO holders redeem $1)
// All resolved atomically
```

> ⚠️ **Important**: `resolveCategory` is called by ADMIN role and resolves ALL markets in the category simultaneously.

---

**Next**: [Fees](fees.md) · [Trading Overview](../trading/overview.md) · [Create Market (Developer)](../developers/create-market.md)
