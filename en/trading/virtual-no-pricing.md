---
description: How NO tokens are priced without a dedicated AMM pool
---

# Virtual NO Pricing

NO tokens do not have their own AMM pool. The Router synthesizes NO trades using the YES/USDC pool and split/merge operations.

## Buy NO

```
1. Split USDC → YES + NO
2. Sell YES into AMM pool
3. Keep NO tokens

User pays USDC → receives NO tokens
```

## Sell NO

```
1. Buy YES from AMM pool
2. Merge YES + NO → USDC
3. Return USDC to user

User pays NO tokens → receives USDC
```

## Price Relationship

Because of the split/merge anchor:

```
NO price ≈ $1.00 - YES price

If YES trades at $0.70:
  NO ≈ $0.30
```

This is enforced by arbitrage — any deviation creates profit opportunities that push prices back.

## From the User's Perspective

The Router handles all complexity internally. Users simply call:

```typescript
// Buy NO — just like buying YES
await router.buyNo(marketId, usdcIn, mintAmount, minNoOut, recipient, deadline);

// Sell NO — just like selling YES
await router.sellNo(marketId, noIn, minUsdcOut, recipient, deadline);
```

> ⚠️ **Gas cost**: NO trades involve extra steps (split + swap or swap + merge), so gas is approximately 2.5x higher than YES trades. The CLOB supports direct NO trading without this overhead.

---

**Next**: [Market Orders](market-orders.md) · [Smart Routing](smart-routing.md) · [Fees](../concepts/fees.md)
