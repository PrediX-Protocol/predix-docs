---
description: Integration patterns theo persona — App Builder, Market Maker, Bot, Protocol Developer, Aggregator.
---

# Integration Patterns

4 persona chính + 1 advanced. Mỗi persona có pattern riêng.

---

## For App Builders

Bạn build FE / widget / dApp embed, user trade qua wallet extension.

### Pattern

```
User wallet ─┬─> Your FE ─> BE API (read data, auth)
             │
             └─> Router contract (place trade, direct sign)
```

### Stack

- **Data fetch**: BE REST v2 (`openapi-fetch` + types)
- **Auth**: SIWE challenge/verify → session token
- **Write**: viem `writeContract` → Router/Exchange/Diamond
- **Event**: WebSocket RPC subscribe Router `Trade` events cho live UX (optional)

### Essential flow

1. User connect wallet (wagmi / RainbowKit)
2. Detect chain = Unichain Sepolia; prompt switch nếu sai
3. SIWE sign-in → localStorage session token
4. Fetch markets qua `apiClient.GET('/api/v2/markets')`
5. User click "Buy YES" → show quote via `apiClient.GET('/api/v2/markets/:id/pricing/quote')`
6. User approve USDC (1 time) → `writeContract(USDC, approve, [ROUTER, MaxUint256])`
7. User confirm trade → `writeContract(ROUTER, buyYes, args)`
8. Wait receipt → show success + refetch portfolio

### Checklist

- [ ] Type-safe API client từ OpenAPI spec
- [ ] Error boundary cho BE 4xx/5xx
- [ ] Auth interceptor với 401 → re-prompt SIWE
- [ ] Transaction simulation trước submit (reveal revert)
- [ ] Toast UI cho tx lifecycle (pending / success / error)
- [ ] Handle `Market_Ended`, slippage, deadline errors gracefully
- [ ] Refetch portfolio + market state sau mỗi tx confirm
- [ ] Locale fallback cho `LocalizedString`
- [ ] Lowercase address compare everywhere

### Reference implementations

- Official FE: [github.com/predix-protocol/frontend](https://github.com/predix-protocol)
- Starter template (Q2 2026): `github.com/predix-protocol/examples/app-starter`

---

## For Market Makers

Bạn cung cấp liquidity cho AMM pool + post limit orders trên CLOB, thu maker fees.

### Pattern

```
Your bot ─┬─> v4 PositionManager (add/remove AMM liquidity)
          │
          ├─> Exchange (post limit orders)
          │
          └─> On-chain event subscribe (fill monitoring)
```

### Stack

- **No BE dependency** — talk trực tiếp chain (BE cache có lag)
- **Low-latency RPC**: dùng WebSocket URL, dedicated RPC (Alchemy, QuickNode paid tier)
- **Event subscribe**: `watchContractEvent` cho Exchange `OrderMatched`, Hook `Hook_MarketTraded`
- **Inventory management**: track YES + NO balance per market trong Redis / PostgreSQL
- **Risk**: dynamic fee tăng khi gần endTime — reposition hoặc exit

### Essential flow

#### Post limit orders (CLOB)

```typescript
// 1. Split USDC → YES + NO inventory
await diamond.write.splitPosition([marketId, usdcAmount]);

// 2. Post bid
await exchange.write.placeOrder([marketId, Side.BUY_YES, priceBid, amountBid]);

// 3. Post ask
await exchange.write.placeOrder([marketId, Side.SELL_YES, priceAsk, amountAsk]);

// 4. Subscribe OrderMatched cho fills
exchange.watchEvent.OrderMatched({
  onLogs: handleFill,
});
```

#### Provide AMM liquidity (v4)

Via Uniswap v4 PositionManager (chưa documented rộng — xem [v4 docs](https://docs.uniswap.org/contracts/v4)).

### Checklist

- [ ] Target spread per market (ví dụ 1% around mid)
- [ ] Max inventory skew (stop posting nếu +90% YES / -10% NO)
- [ ] Kill switch khi market < 1 day to end (dynamic fee 5%)
- [ ] Monitor `OrderMatched` < 1s latency (WebSocket required)
- [ ] Cancel + re-post khi price drift > X bps
- [ ] Account cho MINT/MERGE surplus — có thể net profit từ dual-side orders
- [ ] Handle INV-2 solvency edge cases

### Advanced: MINT surplus farming

Kiểu MM "dual-side" post BUY_YES + BUY_NO với tổng > $1 → trigger MINT matching → protocol mint cặp mới, surplus = fee protocol. Nhưng bạn vẫn "buy at net $1 per pair" → capture IV arbitrage nếu spread khớp.

---

## For Bots / Algo Traders

Bạn run algo: arb, momentum, reversion, news-driven.

### Pattern

```
Your algo ─┬─> BE API (market metadata, candles, history)
           │
           ├─> Indexer REST (aggregated stats) — optional, BE đủ
           │
           ├─> Router (execute trades)
           │
           └─> On-chain event subscribe (fast price feed)
```

### Stack

- **Data source mix**: BE cho UI data + portfolio; on-chain events cho price feed
- **Strategies**: price deviation from fair value; mean reversion post-news; arb giữa 2 markets linked qua event
- **Gas mgmt**: Unichain L2 cheap nhưng vẫn count — batch via multicall
- **Nonce**: viem auto-handles, nhưng watch cho reorg-driven nonce gap

### Essential flow

```typescript
// 1. Subscribe all Trade events
publicClient.watchContractEvent({
  address: ROUTER,
  abi: RouterAbi,
  eventName: 'Trade',
  onLogs: async (logs) => {
    for (const log of logs) {
      const fairValue = await computeFairValue(log.args.marketId);
      const actualPrice = log.args.yesPrice;
      if (Math.abs(fairValue - actualPrice) > THRESHOLD) {
        await executeArb(log.args.marketId, fairValue, actualPrice);
      }
    }
  },
});
```

### Checklist

- [ ] WebSocket RPC cho event subscribe < 1s latency
- [ ] Rate limit monitor for BE (stay under 300 req/min session)
- [ ] Pre-approve USDC + YES + NO với MaxUint256 cho speed
- [ ] Simulate trades (`simulateContract`) trước submit — avoid wasted gas
- [ ] Max position size cap (per market + protocol-wide)
- [ ] Circuit breaker: stop trading nếu N errors consecutive
- [ ] Log every tx hash + outcome cho post-mortem
- [ ] Private RPC (Alchemy dedicated) cho priority mempool

---

## For Protocol Developers

Bạn build trên PrediX: custom oracle, hook variant, aggregator, composable protocol.

### Custom Oracle

Implement `IOracle`:

```solidity
contract MyOracle is IOracle {
    function getResolution(uint256 marketId) external view returns (bool, bool) {
        // your logic
    }
}
```

Admin call `diamond.setApprovedOracle(myOracle, true)`. Xem [Oracle](../smart-contracts/32-oracle.md).

### Hook Variant

Fork `PrediXHookV2` bytecode, modify logic (vd: thêm custom fee curve). Deploy with mined salt để match `getHookPermissions()`. Register qua Diamond.

Warning: hook variant không được official support. Test kỹ + audit trước khi deploy.

### Aggregator

Build on top Router — your contract chọn Route giữa PrediX + competitor (nếu có):

```solidity
contract MyAggregator {
    IPrediXRouter immutable predixRouter;
    // IOtherRouter otherRouter;

    function buyBest(...) external {
        uint256 predixQuote = predixRouter.quoteBuyYes(marketId, amount);
        // uint256 otherQuote = otherRouter.quote(...)
        if (predixQuote > otherQuote) {
            predixRouter.buyYes(marketId, amount, minOut, recipient, deadline);
        } else {
            // route other
        }
    }
}
```

### Composable DeFi

Outcome tokens là ERC-20 → dùng như collateral:

- **LP**: cung cấp YES/USDC liquidity trên 3rd-party DEX
- **Lending**: deposit YES vào Aave / Morpho (nếu support)
- **Vault**: tạo yield vault với strategy "always long YES of /top PM markets"

Outcome tokens có `permit2` (ERC-2612) → gasless approval compatible.

---

## For Aggregators (Advanced)

Quote-revert pattern cho efficient quote batching:

```solidity
try router.quoteBuyYes(marketId, usdcIn) {} catch (bytes memory err) {
    uint256 quoteResult = abi.decode(err, (uint256));
    // use quote
}
```

Integration:

- **1inch / LI.FI / Jumper**: submit Router API cho listing (contact partnerships)
- **Portfolio trackers** (Zapper, DeBank): consume Indexer REST cho user positions

Subscribe [Changelog](../tai-nguyen/changelog.md) để biết khi API stable.

---

## Common gotchas

1. **marketId là uint256, BE serialize thành hex hoặc decimal** — tùy endpoint, kiểm tra carefully.
2. **Price decimal string, không number** — BigNumber / decimal.js.
3. **Address lowercase** everywhere — compare strict.
4. **Deadline unix seconds**, không millis.
5. **Slippage buffer** — 2% default cho AMM, 0% cho CLOB (price đã locked).
6. **Permit2 nonce management** — tránh nonce collision.
7. **Reorg**: wait N confirmations cho high-value decisions.
8. **`Hook_IdentityCommitMissing`**: nếu gặp → bạn đang call pool trực tiếp, phải qua Router.
9. **`Router_NonZeroBalance`**: critical invariant violation — report security.
10. **Gas cho complex route**: `buyNo` expensive hơn `buyYes` vì virtual pricing (split + swap).
