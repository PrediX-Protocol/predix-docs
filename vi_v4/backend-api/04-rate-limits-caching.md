---
description: Rate limit per-IP và per-session. Cache 2 tier: hot (2s) + warm (60s).
---

# Rate Limits & Caching

## Rate Limits

BE apply rate limit ở 3 level:

| Level | Limit | Window | Key |
|---|---|---|---|
| **Global per IP** | 1000 req | 1 phút | Client IP |
| **Per session** (authenticated) | 300 req | 1 phút | Session token |
| **Per endpoint sensitive** (quote, SIWE verify) | 60 req | 1 phút | IP + endpoint |

Request vượt giới hạn → `429 Too Many Requests`:

```json
{
  "error": {
    "code": "rate_limit_exceeded",
    "message": "Rate limit exceeded. Retry after 30s.",
    "details": [{ "retryAfterSeconds": 30 }]
  },
  "meta": { ... }
}
```

Response cũng set headers:

```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 42
X-RateLimit-Reset: 1776090060
Retry-After: 30
```

### Bot usage

Bot khuyến khích:

1. Tôn trọng `X-RateLimit-Remaining` + back off khi còn ít
2. Implement exponential backoff khi gặp 429
3. Cache kết quả ở client side cho data không thay đổi liên tục
4. Dùng WebSocket (khi available) thay vì polling aggressive

### Higher-tier access

Integrator cần higher limit (market maker, aggregator) — contact `partnerships@predixpro.io` để request API key với elevated limit.

---

## Caching — 2 tier duy nhất

BE dùng `lru-cache` với **đúng 2 TTL**:

| Tier | TTL | Dùng cho |
|---|---|---|
| **Hot** | 2 giây | Markets list/detail, orderbook, trades, portfolio, orders, pricing quote |
| **Warm** | 60 giây | Stats, category lists, search results, profile, leaderboard |

{% hint style="info" %}
**Tại sao chỉ 2 tier?** Simplicity. Nếu cần "5s" → dùng hot (accept slight staleness). Nếu cần "5 phút" → materialize ra Mongo collection (async refresh job). Không có tier thứ 3.
{% endhint %}

### Cache keys

Cache key namespace:

```
markets:list:<filters_hash>         → hot
markets:detail:<marketId>           → hot
markets:orderbook:<marketId>        → hot
markets:trades:<marketId>:<paging>  → hot
users:portfolio:<address>           → hot
users:orders:<address>:<paging>     → hot
stats:protocol                      → warm
traders:leaderboard:<period>        → warm
```

### Cache invalidation (admin writes)

Mọi admin endpoint (`POST /api/v2/admin/*`) **bắt buộc** invalidate cache sync trước khi return:

| Admin action | Invalidate |
|---|---|
| `POST /admin/markets/:id/display` | `markets:detail:<id>`, `markets:list:*` |
| `POST /admin/markets/:id/pause` | `markets:detail:<id>`, `markets:list:*` |
| `POST /admin/events` | `events:*` |
| `POST /admin/market-metadata` | `markets:detail:*`, `markets:list:*` |

Missing invalidation = Bug #2 → reject PR.

### Response cache headers

Endpoint hot:

```
Cache-Control: public, max-age=2
```

Endpoint warm:

```
Cache-Control: public, max-age=60
```

FE/bot có thể tự cache tương ứng.

### Example staleness

User request `/api/v2/markets/0xabc` 2 lần trong 1 giây:

- Lần 1: BE fetch từ Indexer → cache (2s) → return
- Lần 2 (trong 2s): BE return cache trực tiếp → có thể stale ~1-2s

Cho critical path (place trade, post order), FE **không** trust cache — gọi `Router.quote*` on-chain trực tiếp ngay trước khi submit tx, bypass BE cache.

## CORS

BE allow CORS cho:

- `https://app.predixpro.io` (FE chính)
- `https://admin.predixpro.io` (FE admin)
- `http://localhost:3000`, `http://localhost:3001` (local dev)

Khác → CORS reject. Contact partnerships cho whitelist domain custom.

## Observability

Mỗi response có headers debug:

```
X-Request-Id: <uuid-v4>           # Trace ID
X-Cache-Status: HIT | MISS | STALE
X-Indexer-Latency-Ms: 42
```

Bao gồm `X-Request-Id` trong bug report để team lookup trace logs.
