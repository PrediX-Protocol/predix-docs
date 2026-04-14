# CLOB Market Making

Provide liquidity on the on-chain order book by placing simultaneous bid and ask limit orders.

## Basic Strategy

Place buy orders below market price and sell orders above it, earning the spread.

```typescript
const EXCHANGE = "0xa202abCb2A358c0862B2dA76b553398339F2C638";

const exchange = new ethers.Contract(EXCHANGE, [
  "function placeOrder(bytes32,uint8,uint256,uint256) returns (bytes32,uint256)",
  "function cancelOrder(bytes32)"
], signer);

// Market YES price is ~$0.55
// Place bid at $0.53, ask at $0.57 → $0.04 spread

// BUY_YES at $0.53
await usdc.approve(EXCHANGE, ethers.parseUnits("1000", 6));
const [bidId] = await exchange.placeOrder(marketId, 0, 530000, ethers.parseUnits("100", 6));

// SELL_YES at $0.57 (need YES tokens)
await yesToken.approve(EXCHANGE, ethers.parseUnits("100", 6));
const [askId] = await exchange.placeOrder(marketId, 1, 570000, ethers.parseUnits("100", 6));
```

## Spread Management

| Spread | Risk Level | Typical Use |
|--------|-----------|-------------|
| $0.01–$0.02 | High (tight) | High-volume markets, competitive MM |
| $0.03–$0.05 | Medium | Standard markets |
| $0.05–$0.10 | Low (wide) | Low-volume or volatile markets |

## Inventory Risk

As orders fill, you accumulate directional exposure:

- Lots of YES filled → you're long YES (bullish exposure)
- Lots of NO filled → you're long NO (bearish exposure)

**Mitigation:** Regularly rebalance by adjusting bid/ask sizes, using split/merge, or trading on the AMM.

## Constraints

- **Max 50 orders** per user per market — manage order count carefully
- **Min order $1.00** — each side must meet minimum
- **Price ticks $0.01** — all prices must be on-tick

## Market Maker Bot

For automated market making, build a bot that:

1. Reads `getBestPrices` and `getDepthAtPrice` for current state
2. Calculates fair value from external signals
3. Places orders at configured spread around fair value
4. Monitors fills via `OrderMatched` events
5. Rebalances inventory when thresholds are exceeded
6. Cancels and replaces stale orders periodically

## Next Steps

- [Liquidity Overview](overview.md) — AMM LP provision
- [Order Book](../trading/order-book.md) — reading CLOB depth
- [Limit Orders](../trading/limit-orders.md) — order mechanics
