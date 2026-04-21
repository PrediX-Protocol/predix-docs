---
description: Trading endpoints — orderbook, trades, quotes. Duplicate key entries also listed in Markets section.
---

# Trading Endpoints

Endpoint liên quan đến trading được nested dưới `/api/v2/markets/{id}/*`. Section này highlight các endpoint dev thường dùng nhất khi build bot/MM.

## Orderbook snapshot

```
GET /api/v2/markets/{id}/orderbook?depth=20
```

Xem [Markets Endpoints](05-endpoints-markets.md#get-apiv2marketsidorderbook--orderbook-snapshot).

**Bot tip**: Poll mỗi 2-5s; tôn trọng `X-Cache-Status` để biết có stale.

## Trades

```
GET /api/v2/markets/{id}/trades?limit=100&since=1776000000
```

Latest trades, ordered desc timestamp.

**Bot tip**: Dùng `since` param để incremental fetch thay vì full refresh.

## Quote trước khi trade

```
GET /api/v2/markets/{id}/pricing/quote?side=YES&action=buy&amountIn=100
```

Response: expected out, price impact, route breakdown.

{% hint style="warning" %}
**BE quote có thể stale**. Cho critical path (submit tx), gọi `Router.quoteBuyYes(...)` on-chain trực tiếp. BE quote chỉ dùng cho UI hiển thị ước tính.
{% endhint %}

## Current pricing

```
GET /api/v2/markets/{id}/pricing/view
```

Snapshot best bid/ask YES/NO + implied probability. Dùng cho UI card.

## Batch pricing (50 markets cùng lúc)

```
GET /api/v2/markets-batch/price-views?ids=0xabc,0xdef,...
```

Giảm round-trip cho UI homepage (list view).

## Trade breakdown

```
GET /api/v2/trades/{txHash}/breakdown
```

Post-trade analysis — cho user xem "tôi trade đã chia giữa CLOB $60 và AMM $40, fee total $0.5".

---

## Real-time alternatives

BE chưa expose WebSocket (on roadmap). Options cho real-time:

### Option 1 — Polling BE (recommended)

```typescript
setInterval(async () => {
  const ob = await apiClient.get(`/markets/${id}/orderbook`);
  updateUI(ob);
}, 2000);
```

Respect `X-RateLimit-Remaining`. Cache hot 2s nên poll < 2s là waste.

### Option 2 — On-chain event subscribe

Dùng viem `watchContractEvent` subscribe Router `Trade` hoặc Exchange `OrderMatched`:

```typescript
publicClient.watchContractEvent({
  address: ROUTER_ADDRESS,
  abi: RouterAbi,
  eventName: 'Trade',
  onLogs: logs => handleNewTrade(logs),
});
```

Chi tiết: [Event Listening](../developers/41-event-listening.md).

### Option 3 — Indexer REST polling

Ponder indexer expose `/api/markets/{id}/trades` — trực tiếp nhanh hơn BE chút. Nhưng FE **không** nên gọi trực tiếp (BE là sole gateway). Bot có thể dùng trong staging/dev.

---

## Place trade — KHÔNG qua BE

**Quan trọng**: Place trade là **on-chain** call qua Router, không qua BE API.

```typescript
// Pseudo
await router.write.buyYes([marketId, usdcIn, minOut, recipient, deadline]);
```

BE chỉ serve read data (orderbook, quotes, portfolio). Mọi write đi thẳng qua wallet → Router/Exchange/Diamond contracts.

Chi tiết code: [Trading Integration](../developers/35-trading-integration.md).

---

## Admin trading endpoints

Admin có các endpoint write (SIWE + role gated):

| Endpoint | Mục đích |
|---|---|
| `POST /api/v2/admin/markets/:id/display` | Update display metadata (title override, icon) |
| `POST /api/v2/admin/markets/:id/pause` | Pause market (module-keyed) |
| `POST /api/v2/admin/markets/:id/resume` | Resume market |
| `POST /api/v2/admin/markets/:id/hide` | Soft-hide (không show in list) |
| `POST /api/v2/admin/markets/:id/unhide` | Unhide |
| `POST /api/v2/admin/markets/:id/outcomes` | Create outcome alias (cho multi/scalar) |

Admin endpoints không public docs chi tiết ở đây. Contact team cho admin onboarding.
