# Exchange Contract

The Exchange implements a fully on-chain Central Limit Order Book (CLOB) with a 99-tick bitmap for efficient price-level management.

## Order Book Structure

- **99 price ticks:** $0.01 to $0.99 in $0.01 steps
- **Bitmap indexing:** O(1) lookup for best price
- **FIFO within price level:** First order at a price gets matched first

## Functions

### placeOrder

```solidity
placeOrder(bytes32 marketId, uint8 side, uint256 price, uint256 amount)
  → (bytes32 orderId, uint256 filledAmount)
```

Places a limit order. May partially fill immediately against resting orders. Returns the `orderId` and any `filledAmount`.

### cancelOrder

```solidity
cancelOrder(bytes32 orderId)
```

Cancel your own order. After market expiry, anyone can cancel any order.

### fillMarketOrder

```solidity
fillMarketOrder(bytes32 marketId, uint8 side, uint256 maxPrice, uint256 amount, address taker, address recipient)
  → (uint256 filledAmount, uint256 totalCost)
```

Execute a market order against resting limit orders. Used internally by the Router.

### View Functions

```solidity
getOrder(bytes32 orderId) → Order
getBestPrices(bytes32 marketId) → (bestBidYes, bestAskYes, bestBidNo, bestAskNo)
getDepthAtPrice(bytes32 marketId, uint8 side, uint256 price) → uint256
getOrderBook(bytes32 marketId, uint8 depth) → (yesBids, yesAsks, noBids, noAsks)
```

### Pause

```solidity
pause()       // PAUSE_ADMIN
unpause()     // PAUSE_ADMIN
setPauseAdmin(address)  // PAUSE_ADMIN
```

## Three-Phase Matching Algorithm

When `placeOrder` is called:

1. **Phase 1 — COMPLEMENTARY:** Match against opposite-side orders (BUY_YES ↔ SELL_YES)
2. **Phase 2 — MINT:** Match BUY_YES + BUY_NO where combined price ≥ $1.00
3. **Phase 3 — MERGE:** Match SELL_YES + SELL_NO where combined price ≤ $1.00

For detailed match type mechanics, see [Matching Engine](../trading/matching-engine.md).

## Next Steps

- [Router](router.md) — how Router uses the Exchange
- [Trading: Order Book](../trading/order-book.md) — reading order book data
- [Trading: Limit Orders](../trading/limit-orders.md) — code examples
