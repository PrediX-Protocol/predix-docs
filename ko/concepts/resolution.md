---
description: How markets are resolved and how winners redeem
---

# Resolution

## Market Lifecycle

```
Created → Active (trading) → Expired → Resolved → Redeemable
                                ↓
                        Emergency (7 days) → Emergency Resolved
                                ↓
                        Refund Mode → Users Refund
```

## Normal Resolution

1. Market reaches `endTime` (trading continues but no new splits)
2. Oracle reports outcome: `resolve(marketId, true/false)`
3. Anyone calls `resolveMarket(marketId)` on the Diamond
4. Market is marked as resolved
5. Winners call `redeemMarketTokens(marketId)`

```typescript
// Step 1: Resolve (anyone can call after oracle reports)
await diamond.resolveMarket(marketId);

// Step 2: Redeem winning tokens
await diamond.redeemMarketTokens(marketId);
// Winners receive 1 USDC per winning token
```

## Emergency Resolution

If the oracle fails to report within 7 days after `endTime`:

1. An `OPERATOR` can call `emergencyResolve(marketId, outcome)`
2. This bypasses the oracle and sets the outcome directly

> ⚠️ Emergency resolution requires a **7-day delay** after `endTime` and can only be called by the OPERATOR role.

## Refund Mode

If a market is deemed invalid or problematic:

1. `OPERATOR` calls `enableRefundMode(marketId)`
2. All trading is blocked (split, merge, CLOB, AMM)
3. Token holders call `refund(marketId)`
4. Each holder receives USDC proportional to their YES + NO balance

```typescript
// Operator enables refund
await diamond.enableRefundMode(marketId);

// Users claim proportional refund
await diamond.refund(marketId);
```

## Category Resolution

For multi-outcome markets:

```typescript
// Admin resolves the category: winner is outcome index 2
await diamond.resolveCategory(categoryId, 2);
// All markets in the category are resolved atomically
```

---

**Next**: [Multi-Outcome Markets](multi-outcome-markets.md) · [Fees](fees.md) · [Oracle Contract](../contracts/oracle.md)
