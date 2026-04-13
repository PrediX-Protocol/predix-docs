---
description: Resolve markets and redeem winning tokens
---

# Resolve & Redeem

## Resolve Market

After the oracle reports the outcome, anyone can finalize:

```typescript
// Check if oracle has reported
const oracle = new ethers.Contract(ORACLE_ADDRESS, ORACLE_ABI, provider);
const [resolved, outcome] = await oracle.getResolution(marketId);

if (resolved) {
  const tx = await diamond.resolveMarket(marketId);
  await tx.wait();
  console.log("Market resolved. Outcome:", outcome ? "YES" : "NO");
}
```

## Redeem Winning Tokens

```typescript
// Winners get 1 USDC per winning token
const tx = await diamond.redeemMarketTokens(marketId);
await tx.wait();
```

## Emergency Resolve (OPERATOR only)

```typescript
// Available 7 days after endTime if oracle hasn't reported
await diamond.emergencyResolve(marketId, true); // true = YES wins
```

## Refund Mode (OPERATOR only)

```typescript
// Enable refund for problematic markets
await diamond.enableRefundMode(marketId);

// Users claim proportional refund
await diamond.refund(marketId);
```

---

**Next**: [Events](events.md) · [Errors](errors.md)
