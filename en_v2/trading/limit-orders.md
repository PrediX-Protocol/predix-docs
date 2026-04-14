# Limit Orders

Limit orders let you specify the exact price you want to trade at. Orders rest on the on-chain CLOB until matched or cancelled.

## Functions

All limit order functions are on the **Exchange** (`0xa202abCb2A358c0862B2dA76b553398339F2C638`).

```solidity
placeOrder(bytes32 marketId, uint8 side, uint256 price, uint256 amount)
  → (bytes32 orderId, uint256 filledAmount)

cancelOrder(bytes32 orderId)
```

## Side Enum

| Value | Side | Meaning |
|-------|------|---------|
| 0 | `BUY_YES` | Bid for YES tokens |
| 1 | `SELL_YES` | Ask for YES tokens |
| 2 | `BUY_NO` | Bid for NO tokens |
| 3 | `SELL_NO` | Ask for NO tokens |

## Price Format

Prices are in USDC with 6 decimals, from $0.01 to $0.99:

| Display Price | On-chain Value | Tick |
|---------------|---------------|------|
| $0.01 | `10000` | Min |
| $0.50 | `500000` | Mid |
| $0.99 | `990000` | Max |

## Constraints

- **Min order:** $1.00 (1,000,000 in 6 decimals)
- **Max orders per user per market:** 50
- **Price ticks:** $0.01 increments only

## Code Example

```typescript
const EXCHANGE = "0xa202abCb2A358c0862B2dA76b553398339F2C638";

const exchange = new ethers.Contract(EXCHANGE, [
  "function placeOrder(bytes32,uint8,uint256,uint256) returns (bytes32,uint256)",
  "function cancelOrder(bytes32)"
], signer);

// Approve USDC to Exchange for buy orders
await usdc.approve(EXCHANGE, ethers.parseUnits("100", 6));

// Place limit order: BUY 100 YES at $0.55
const side = 0; // BUY_YES
const price = 550000; // $0.55 in 6 decimals
const amount = ethers.parseUnits("100", 6); // 100 tokens

const [orderId, filledAmount] = await exchange.placeOrder(marketId, side, price, amount);
console.log(`Order ${orderId} placed. Immediately filled: ${filledAmount}`);

// Cancel the order
await exchange.cancelOrder(orderId);
```

> ⚠️ `placeOrder` may partially fill immediately if matching orders exist. Check `filledAmount` in the return value.

> ⚠️ Anyone can cancel orders on expired markets, not just the order owner.

## Next Steps

- [Matching Engine](matching-engine.md) — how orders are matched
- [Order Book](order-book.md) — reading order book data
- [Market Orders](market-orders.md) — instant execution via Router
