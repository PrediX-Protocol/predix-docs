---
description: Place and manage limit orders on the on-chain CLOB
---

# Limit Orders

Limit orders are placed directly on the PrediXExchange (CLOB). They remain on-chain until filled, cancelled, or the market expires.

## Place Order

```typescript
const exchange = new ethers.Contract(
  "0xa202abCb2A358c0862B2dA76b553398339F2C638",
  EXCHANGE_ABI,
  signer
);

// Side enum: 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
const side = 0; // BUY_YES
const price = ethers.parseUnits("0.65", 6);   // $0.65 per token
const amount = ethers.parseUnits("100", 6);    // 100 tokens

// For BUY orders: approve USDC to Exchange
// deposit = price × amount / 1e6
await usdc.approve(exchange.target, price * amount / 1000000n);

const tx = await exchange.placeOrder(marketId, side, price, amount);
const receipt = await tx.wait();
// Parse OrderPlaced event for orderId
```

## Cancel Order

```typescript
await exchange.cancelOrder(orderId);
// Unfilled deposit is returned to the order owner
```

> ⚠️ Anyone can cancel expired market orders (after endTime).

## Constraints

| Parameter | Value |
| --------- | ----- |
| Price range | $0.01 – $0.99 (`10000` – `990000` in 6 decimals) |
| Price step | $0.01 (`10000`) |
| Min order amount | $1.00 (`1000000` in 6 decimals) |
| Max orders per user per market | 50 |
| Max fills per placement | 20 |
| Self-trade prevention | maker.owner ≠ taker.owner |

## Side Enum

```solidity
enum Side {
    BUY_YES,   // 0
    SELL_YES,  // 1
    BUY_NO,    // 2
    SELL_NO    // 3
}
```

---

**Next**: [Matching Engine](matching-engine.md) · [Smart Routing](smart-routing.md) · [Order Book](order-book.md)
