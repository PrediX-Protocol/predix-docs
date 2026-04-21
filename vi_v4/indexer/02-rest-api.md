---
description: Indexer Hono REST API — endpoint cho BE consumer + self-host ops.
---

# Indexer REST API

Indexer expose REST endpoint qua Hono framework. Chủ yếu dùng bởi BE `IndexerClient`.

{% hint style="warning" %}
**FE/bot không được gọi thẳng Indexer API**. Luôn đi qua BE `/api/v2/*`. Lý do: BE apply auth, cache, envelope, admin overlays. Indexer chỉ expose raw indexed data.
{% endhint %}

## Base URL

| Environment | Base URL |
|---|---|
| Internal testnet | `http://indexer.internal:42069` (VPC-only) |
| Local dev | `http://localhost:42069` |

## Envelope convention

Khác BE, Indexer envelope tối giản hơn:

- **List** endpoint: `{ data: T[], meta: { limit, offset, count } }`
- **Single** endpoint: `{ data: T | null, meta: null }`
- **Health / status** endpoint: **flat** `{ status, latestIndexedBlock, lagBlocks }` — KHÔNG wrap

## Endpoints

### `GET /api/markets`

Query: `limit`, `offset`, `sortBy` (`newest` \| `volume` \| `ending_soon`), `eventId`, `status`, `resolved`.

Response:

```json
{
  "data": [
    {
      "id": "0xabc...",
      "question": "Will BTC > $100K?",
      "endTime": 1789200000,
      "yesToken": "0x...",
      "noToken": "0x...",
      "eventId": "0",
      "totalCollateral": "1500000000",
      "volume": "15234500000",
      "tradeCount": 892,
      "isResolved": false,
      "resolutionType": "NONE"
    }
  ],
  "meta": { "limit": 20, "offset": 0, "count": 145 }
}
```

### `GET /api/markets/{id}`

Response: single market row.

### `GET /api/markets/{id}/trades`

Union của `trade` + projection của `takerFill` — để CLOB direct fills xuất hiện cạnh Router trades.

Query: `limit`, `offset`, `since`, `until`.

### `GET /api/markets/{id}/positions/{address}`

Position của user trong market cụ thể.

### `GET /api/events`

Query: `limit`, `offset`, `status`.

Response: event_group list.

### `GET /api/events/{id}`

Single event group với member market list.

### `GET /api/stats`

**Flat** (no envelope) — singleton protocol stats:

```json
{
  "totalMarkets": 145,
  "totalVolume": "1500000000000",
  "totalTrades": 8934,
  "totalFeesCollected": "15000000000",
  "totalUsers": 1234,
  "lastUpdated": 1776089500
}
```

### `GET /api/portfolio/{address}`

User portfolio: positions array + userStats.

```json
{
  "data": {
    "address": "0xabc...",
    "positions": [...],
    "stats": {
      "totalVolume": "...",
      "tradeCount": ...,
      "winRate": "0.62",
      "currentStreak": 3
    }
  },
  "meta": null
}
```

### `GET /api/leaderboard`

Query: `sortBy` (`volume` \| `winRate` \| `streak`), `limit`, `offset`.

Response: top traders.

### Ponder reserved paths

Ponder framework reserve:

- `GET /health` — liveness
- `GET /ready` — readiness
- `GET /status` — sync status + latest block
- `GET /metrics` — Prometheus metrics
- `GET /graphql` — auto-generated GraphQL (available nhưng không dùng chính thức)

Custom routes của PrediX phải dùng `/api/*` prefix để không conflict.

## Self-host

Xem [Reorg & Finality](04-reorg-finality.md) cho chi tiết cấu hình. Quick start:

```bash
git clone https://github.com/predix-protocol/indexer
cd indexer
cp testenv.local.example testenv.local
# edit testenv.local với RPC + contract addresses

pnpm install
pnpm codegen
docker-compose up -d postgres
pnpm dev
```

Indexer sẽ backfill từ `START_BLOCK` → latest, sau đó live-index.

## Monitoring

Metrics exposed at `/metrics`:

- `ponder_sync_block_number` — current indexed block
- `ponder_sync_lag_seconds` — lag so với latest
- `ponder_handler_duration_seconds{handler}` — handler perf
- `ponder_rpc_request_count{method}` — RPC call volume

Alert khi `sync_lag_seconds > 60` sustained 5 phút.
