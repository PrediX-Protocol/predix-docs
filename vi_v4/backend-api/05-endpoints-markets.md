---
description: Markets endpoints — list, detail, orderbook, trades, prices, holders, activity, comments.
---

# Markets Endpoints

## `GET /api/v2/markets` — List

Query:

| Param | Kiểu | Mô tả |
|---|---|---|
| `kind` | enum | `binary` \| `scalar` \| `multi` \| `sports` \| `grouped` |
| `status` | enum | `active` \| `resolved` \| `refunding` |
| `eventId` | string | Filter children của event |
| `isFeatured` | bool | Featured markets |
| `tag` | string | Tag do admin gán |
| `categoryId` | string | Category ID |
| `q` | string | Full-text search (1-200 chars) |
| `sortBy` | enum | `newest` \| `volume` \| `ending_soon` \| `featured` \| `trending` |
| `includeEventChildren` | bool | Include event children (default `false`) |
| `limit` | int (≤100) | Default 20 |
| `offset` | int | Default 0 |

**Default behavior** (no flag): exclude markets với `eventId != 0` (child markets). Canonical access cho children: `/events/:id` hoặc `/markets?eventId=X` hoặc `/markets?includeEventChildren=true`.

Response (truncated):

```json
{
  "data": [
    {
      "marketId": "0xabc...",
      "kind": "binary",
      "title": { "key": "market.0xabc.title", "fallback": "Will BTC > $100K?" },
      "status": "active",
      "endTime": 1789200000,
      "binary": {
        "yesPrice": "0.524",
        "noPrice": "0.476",
        "yesToken": "0x...",
        "noToken": "0x..."
      },
      "volume": { "decimal": "15234.5", "raw": "15234500000", "decimals": 6, "unit": "USDC" },
      "tradeCount": 892,
      "capabilities": ["trade", "limit", "split", "merge"]
    }
  ],
  "meta": {
    "timestamp": 1776090000,
    "version": "v2",
    "pagination": { "limit": 20, "offset": 0, "total": 145 }
  }
}
```

Cache: **hot (2s)**.

---

## `GET /api/v2/markets/{id}` — Detail

Response: full market envelope bao gồm statuses, capabilities, metadata overlay (title, description, icon, tags).

Cache: **hot (2s)**.

---

## `GET /api/v2/markets/{id}/orderbook` — Orderbook snapshot

Query:

| Param | Kiểu | Default |
|---|---|---|
| `depth` | int | 20 (levels) |

Response:

```json
{
  "data": {
    "marketId": "0xabc...",
    "bids": [
      { "side": "YES", "price": "0.52", "amount": { "decimal": "150.0", ... } },
      { "side": "YES", "price": "0.51", "amount": { "decimal": "200.0", ... } }
    ],
    "asks": [
      { "side": "YES", "price": "0.54", "amount": { "decimal": "100.0", ... } }
    ],
    "spread": "0.02",
    "midPrice": "0.53"
  },
  "meta": { ... }
}
```

Cache: **hot (2s)**.

---

## `GET /api/v2/markets/{id}/trades` — Trade history

Query: `limit`, `offset`, `since`, `until`, `side` (`YES` | `NO`).

Response: array of trades (ordered desc by timestamp).

```json
{
  "data": [
    {
      "txHash": "0x...",
      "trader": "0xabc...",
      "side": "YES",
      "amountIn": { "decimal": "100", ... },
      "amountOut": { "decimal": "192.3", ... },
      "yesPrice": "0.52",
      "clobFilled": { "decimal": "60", ... },
      "ammFilled": { "decimal": "40", ... },
      "timestamp": 1776089500,
      "tradeType": "BUY_YES"
    }
  ],
  "meta": { ... }
}
```

Cache: **hot (2s)**.

---

## `GET /api/v2/markets/{id}/prices` — Price history

Query: `since`, `until`, `interval` (`1m` | `5m` | `1h` | `1d`).

Response: array of price snapshots cho chart.

Cache: **warm (60s)**.

---

## `GET /api/v2/markets/{id}/candles` — OHLCV candles

Query: `interval` (`1m` | `5m` | `15m` | `1h` | `4h` | `1d`), `since`, `until`.

Response:

```json
{
  "data": [
    {
      "timestamp": 1776086400,
      "open": "0.48",
      "high": "0.53",
      "low": "0.47",
      "close": "0.52",
      "volume": { "decimal": "1234.5", ... },
      "tradeCount": 45
    }
  ],
  "meta": { ... }
}
```

Cache: **warm (60s)**.

---

## `GET /api/v2/markets/{id}/holders` — Token holders

Query: `side` (`YES` | `NO`), `limit`, `offset`, `sortBy` (`balance` | `recent`).

Response: list of top holders.

Cache: **warm (60s)**.

---

## `GET /api/v2/markets/{id}/activity` — Activity feed

Kết hợp trades + orders + splits/merges + redeems sắp xếp theo time desc.

Cache: **hot (2s)**.

---

## `GET /api/v2/markets/{id}/comments` — Comments

User-generated comment feed (requires Mongo overlay).

Cache: **hot (2s)**. Write endpoints yêu cầu SIWE.

---

## `GET /api/v2/markets/{id}/related` — Related markets

Gợi ý markets liên quan dựa trên: cùng event, cùng category, overlapping tags.

Cache: **warm (60s)**.

---

## `GET /api/v2/markets/{id}/pricing/quote` — Quote (before trade)

Query:

| Param | Kiểu |
|---|---|
| `side` | `YES` \| `NO` |
| `action` | `buy` \| `sell` |
| `amountIn` | decimal string |

Response: expected `amountOut`, `priceImpact`, `route` (CLOB/AMM split).

Cache: **hot (2s)**.

---

## `GET /api/v2/markets/{id}/pricing/view` — Current pricing state

Response: current best prices, implied probability, orderbook summary.

Cache: **hot (2s)**.

---

## `GET /api/v2/markets-batch/price-views` — Batch pricing

Query: `ids` (comma-separated marketIds, max 50).

Response: array of price views — tiết kiệm round-trip cho UI hiển thị nhiều card.

Cache: **hot (2s)**.

---

## `GET /api/v2/trades/{txHash}/breakdown` — Detailed trade breakdown

Given a `txHash`, return chi tiết route splits (CLOB vs AMM), fees paid, price impact.

Cache: **warm (60s)**.
