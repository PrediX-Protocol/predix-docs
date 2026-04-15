# 31. Hook (Uniswap v4)

## Permissions
beforeInitialize, afterInitialize
beforeSwap, afterSwap

## Dynamic Fee Calculation
Phí thay đổi theo thời gian còn lại: 0,5% → 1% → 2% → 5%.

## Anti-Sandwich Protection
Unichain 200ms Flashblocks + TEE ordering. Hook kiểm tra trusted router.

## Pool Registration
registerMarketPool(marketId, yesToken, poolKey) -Diamond only
setTrustedRouter(router, trusted) -Diamond only
initialize(diamond, usdc, admin) -ONCE
getMarketForPool(poolId) → bytes32
