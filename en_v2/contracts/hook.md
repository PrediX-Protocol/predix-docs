# Hook Contract

The Hook is a Uniswap v4 Hook that powers the AMM side of PrediX, implementing custom logic at pool lifecycle points.

## Hook Permissions

| Hook Point | Enabled | Purpose |
|------------|---------|---------|
| `beforeInitialize` | ✅ | Validate pool registration |
| `afterInitialize` | ✅ | Store pool-market mapping |
| `beforeSwap` | ✅ | Calculate dynamic fee, anti-sandwich check |
| `afterSwap` | ✅ | Emit trade events, update metrics |

## Dynamic Fee Calculation

The Hook overrides Uniswap v4's fee at `beforeSwap` based on time remaining before market expiry:

```
if timeToExpiry > 7 days  → fee = 0.5%
if timeToExpiry > 3 days  → fee = 1.0%
if timeToExpiry > 1 day   → fee = 2.0%
if timeToExpiry < 24 hours → fee = 5.0%
```

See [Fees](../concepts/fees.md) for the full fee schedule and rationale.

## Anti-Sandwich Protection

- **Unichain Flashblocks (200ms):** Extremely short block times reduce MEV window
- **TEE ordering:** Unichain's Trusted Execution Environment reorders transactions fairly
- **Dynamic fees near expiry:** 5% fee makes sandwich attacks unprofitable

## Functions

### Registration (Diamond only)

```solidity
registerMarketPool(bytes32 marketId, address yesToken, PoolKey key)
```

Called by Diamond during `createMarket()` to register the YES/USDC pool. Only the Diamond contract can call this.

### Configuration (Diamond only)

```solidity
setTrustedRouter(address router, bool trusted)
```

Only trusted Routers can execute swaps through the Hook. Prevents unauthorized access.

### Initialization (Once)

```solidity
initialize(address diamond, address usdc, address admin)
```

One-time setup linking the Hook to Diamond and USDC.

### View

```solidity
getMarketForPool(PoolId poolId) → bytes32 marketId
```

## Next Steps

- [Oracle](oracle.md) — resolution adapters
- [Router](router.md) — how Router interacts with the Hook
- [Liquidity Overview](../liquidity/overview.md) — providing AMM liquidity
