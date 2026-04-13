---
description: On-chain CLOB with 99-tick bitmap and three-phase matching
---

# Exchange Contract

The PrediXExchange implements an on-chain Central Limit Order Book (CLOB) with a 99-tick bitmap for gas-efficient price scanning.

## Core Functions

### Place Order

```solidity
placeOrder(bytes32 marketId, uint8 side, uint256 price, uint256 amount)
    → (bytes32 orderId, uint256 filledAmount)
```

Attempts to match against existing orders. Unfilled portion becomes a resting order.

### Cancel Order

```solidity
cancelOrder(bytes32 orderId)
```

Owner can cancel anytime. Anyone can cancel after market expiry.

### Fill Market Order

```solidity
fillMarketOrder(bytes32 marketId, uint8 side, uint256 maxPrice,
                uint256 amount, address taker, address recipient)
    → (uint256 filledAmount, uint256 totalCost)
```

Used by the Router for CLOB portion of hybrid execution.

## View Functions

```solidity
getOrder(bytes32 orderId) → Order
getBestPrices(bytes32 marketId) → (bestBidYes, bestAskYes, bestBidNo, bestAskNo)
getDepthAtPrice(bytes32 marketId, uint8 side, uint256 price) → uint256
getOrderBook(bytes32 marketId, uint8 depth) → (yesBids, yesAsks, noBids, noAsks)
```

## Matching Algorithm

Orders are matched in three phases:

1. **COMPLEMENTARY**: Direct counterparty (BUY_YES ↔ SELL_YES)
2. **MINT**: Two buy orders (BUY_YES + BUY_NO, sum ≥ $1.00)
3. **MERGE**: Two sell orders (SELL_YES + SELL_NO, sum ≤ $1.00)

## Constraints

| Parameter | Value |
| --------- | ----- |
| Price range | `10000` – `990000` ($0.01 – $0.99, 6 decimals) |
| Price step | `10000` ($0.01) |
| Min order | `1000000` ($1.00) |
| Max orders/user/market | 50 |
| Max fills per placement | 20 |

## Pause Mechanism

```solidity
pause()            // PAUSE_ADMIN only
unpause()          // PAUSE_ADMIN only
setPauseAdmin(address) // Current PAUSE_ADMIN only
```

---

**Next**: [Router](router.md) · [Matching Engine](../trading/matching-engine.md) · [Limit Orders](../trading/limit-orders.md)
