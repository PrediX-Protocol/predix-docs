# 29. Exchange (CLOB)

## On-Chain Order Book

99-tick bitmap ($0,01–$0,99). Mỗi tick = 10000 đơn vị (6 decimals). Orders lưu trên-chain.

## Functions

```
placeOrder(marketId, side, price, amount) → (orderId, filledAmount)
cancelOrder(orderId) — owner or anyone if market expired
fillMarketOrder(marketId, side, maxPrice, amount, taker, recipient) → (filledAmount, totalCost)
```

## View Functions

```solidity
getOrder(orderId) → Order
getBestPrices(marketId) → (bestBidYes, bestAskYes, bestBidNo, bestAskNo)
getDepthAtPrice(marketId, side, price) → uint256
getOrderBook(marketId, depth) → (yesBids, yesAsks, noBids, noAsks)
```

## Matching Algorithm (3 Phases)

- **Phase 1 — COMPLEMENTARY:** Khớp trực tiếp BUY↔SELL cùng outcome
- **Phase 2 — MINT:** Ghép BUY_YES+BUY_NO khi tổng ≥ $1
- **Phase 3 — MERGE:** Ghép SELL_YES+SELL_NO khi tổng ≤ $1

## Pause Mechanism

```
pause() / unpause() / setPauseAdmin(address) — PAUSE_ADMIN
```
