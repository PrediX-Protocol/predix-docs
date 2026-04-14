# Errors

Custom errors returned by PrediX contracts. All errors are Solidity custom errors (gas-efficient, no string messages).

## Common Errors

| Error | Contract | Cause | Fix |
|-------|----------|-------|-----|
| `Market_NotFound` | Diamond | Invalid `marketId` | Verify market exists: `isMarketExists(marketId)` |
| `Market_Ended` | Diamond/Exchange | Market past `endTime` | Check `endTime` before trading |
| `Market_NotEnded` | Diamond | Trying to resolve before `endTime` | Wait until `endTime` passes |
| `Market_AlreadyResolved` | Diamond | Market already resolved | Check `isResolved` in MarketData |
| `Market_NotResolved` | Diamond | Trying to redeem before resolution | Wait for `resolveMarket()` |
| `Market_ExceedsPerTradeCap` | Diamond | Trade too large | Reduce amount or check `getSafetyCaps()` |
| `Market_ExceedsTvlCap` | Diamond | Protocol TVL limit reached | Wait for TVL to decrease |
| `Market_ExceedsPerMarketCap` | Diamond | Market collateral limit reached | Reduce amount |
| `InvalidPrice` | Exchange | Price not on $0.01 tick (10000–990000) | Use valid price: multiples of 10000 |
| `MaxOrdersExceeded` | Exchange | 50 orders/user/market limit | Cancel existing orders first |
| `MarketNotActive` | Exchange | Market expired, resolved, or in refund mode | Check market status |
| `InsufficientBalance` | ERC-20 | Not enough tokens | Check `balanceOf` before trading |
| `InsufficientAllowance` | ERC-20 | Need to `approve` first | Call `approve(spender, amount)` |
| `DeadlineExpired` | Router | Transaction too slow | Increase deadline or retry |
| `SlippageExceeded` | Router | Output below `minOut` | Increase slippage tolerance |
| `OracleNotApproved` | Diamond | Oracle not whitelisted | Admin must call `setApprovedOracle` |
| `RefundMode_Active` | Diamond | Market in refund mode, can't trade/resolve | Use `refund()` instead |
| `NotOperator` | Diamond | Caller lacks OPERATOR_ROLE | Use correct signer |
| `EmergencyNotReady` | Diamond | <7 days since endTime for emergency resolve | Wait 7 days after endTime |

## Error Handling Pattern

```typescript
try {
  const tx = await router.buyYes(marketId, usdcIn, minOut, recipient, deadline);
  await tx.wait();
} catch (error: any) {
  const reason = error.reason || error.message;

  // Map to user-friendly messages
  const errorMap: Record<string, string> = {
    "Market_NotFound": "This market does not exist.",
    "Market_Ended": "This market has expired. Trading is closed.",
    "SlippageExceeded": "Price moved too much. Try again with higher slippage.",
    "DeadlineExpired": "Transaction took too long. Please retry.",
    "Market_ExceedsPerTradeCap": "Trade is too large. Try a smaller amount.",
  };

  for (const [key, msg] of Object.entries(errorMap)) {
    if (reason.includes(key)) {
      console.error(msg);
      return;
    }
  }
  console.error("Unknown error:", reason);
}
```

## Next Steps

- [Trading Integration](trading-integration.md) — full integration guide
- [Events](events.md) — monitoring events for debugging
- [Security](../contracts/security.md) — safety caps and limits
