---
description: Uniswap v4 Hook — dynamic fees, anti-sandwich, pool management
---

# Hook Contract

PrediXHookV1 is a Uniswap v4 Hook that adds prediction-market-specific logic to AMM pools.

## Hook Permissions

| Hook Point | Enabled | Purpose |
| ---------- | ------- | ------- |
| `beforeInitialize` | ✅ | Validate pool registration |
| `afterInitialize` | ✅ | Setup pool metadata |
| `beforeSwap` | ✅ | Dynamic fee + anti-sandwich check |
| `afterSwap` | ✅ | Record swap data, emit events |

## Dynamic Fee Calculation

Fees increase as markets approach expiry to protect LPs from informed traders:

| Time Remaining | Fee |
| -------------- | --- |
| > 7 days | 0.5% |
| 3–7 days | 1.0% |
| 1–3 days | 2.0% |
| < 24 hours | 5.0% |

## Anti-Sandwich Protection

The Hook tracks each swap as `(block.number, direction)` per user per pool. If the same user swaps in opposite directions within the same block, the transaction reverts with `SandwichDetected()`.

**Trusted Router**: The Hook extracts the actual user address from `hookData` instead of using `msg.sender`, so the Router can execute on behalf of users without triggering false positives.

## Functions

```solidity
// Called by Diamond only
registerMarketPool(bytes32 marketId, address yesToken, PoolKey key)
setTrustedRouter(address router, bool trusted)

// Called once during deployment
initialize(address diamond, address usdc, address admin)

// View
getMarketForPool(PoolId poolId) → bytes32 marketId
```

---

**Next**: [Oracle](oracle.md) · [Fees](../concepts/fees.md) · [Safety](safety.md)
