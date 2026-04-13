---
description: Common errors and how to resolve them
---

# Error Reference

## Common Errors

| Error | Cause | Solution |
| ----- | ----- | -------- |
| `Market_NotFound` | Invalid marketId | Verify marketId from MarketCreated event |
| `Market_Ended` | Market's endTime has passed | Cannot trade after expiry |
| `Market_ExceedsPerTradeCap` | Amount exceeds per-trade safety cap | Reduce trade size |
| `Market_ExceedsTvlCap` | Total system TVL cap reached | Wait or use smaller amount |
| `Market_ExceedsPerMarketCap` | Market's collateral cap reached | Use smaller amount |
| `InvalidPrice` | Price not on $0.01 tick grid | Use multiples of `10000` (6 decimals) |
| `MaxOrdersExceeded` | User has 50 orders in this market | Cancel existing orders first |
| `MarketNotActive` | Market expired, resolved, or in refund mode | Check market status |
| `InsufficientBalance` | Not enough tokens for the operation | Check token balances |
| `SandwichDetected` | Opposite-direction swap in same block | Wait for next block |
| `SlippageExceeded` | Output below minOut | Increase slippage or retry |
| `DeadlineExpired` | Transaction mined after deadline | Set longer deadline |
| `OnlyFactory` | Attempted direct mint/burn of tokens | Tokens can only be minted via Diamond |

## Debugging Tips

```typescript
try {
  await router.buyYes(marketId, usdcIn, minYesOut, recipient, deadline);
} catch (error: any) {
  // Decode custom error
  const iface = new ethers.Interface(ROUTER_ABI);
  try {
    const decoded = iface.parseError(error.data);
    console.error("Custom error:", decoded?.name, decoded?.args);
  } catch {
    console.error("Raw error:", error.message);
  }
}
```

---

**Next**: [Developer Quickstart](quickstart.md) · [Trading Integration](trading-integration.md)
