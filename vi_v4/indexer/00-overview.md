---
description: Indexer — Ponder 0.16 subscribe SC events → PostgreSQL → Hono REST API.
---

# Indexer Overview

PrediX Indexer dùng [Ponder 0.16](https://ponder.sh/) — event-driven indexer framework, TypeScript strict + ESM, deploy với PostgreSQL 16.

## Vai trò

- Subscribe events từ 13 contracts trên Unichain Sepolia (1301 testnet)
- Populate 28 tables PostgreSQL với state đã derive
- Expose Hono REST API `/api/*` cho BE consume
- Fail-loud: handler throw error → Ponder retry, không silent default

## Stack

| Component | Tech |
|---|---|
| Framework | Ponder 0.16.6 |
| Language | TypeScript strict + ESM only |
| DB | PostgreSQL 16 (Docker `predix-postgres` hoặc embedded `pglite`) |
| API | Hono 4.5 + Zod OpenAPI |
| Package manager | pnpm 10 |
| Test | vitest 4 |
| Finalize source | Unichain `finalized` block tag (~12-15 min L2 finality) |

## Kiến trúc

```mermaid
flowchart LR
    SC[Smart Contracts<br/>on Unichain] -->|logs| RPC[Unichain RPC]
    RPC -->|eth_getLogs| Ponder[Ponder Sync Engine]
    Ponder -->|event handlers| Handlers[13 handler files]
    Handlers -->|upsert| PG[(PostgreSQL<br/>28 tables)]
    Handlers -->|aggregate| PG
    API[Hono REST /api/*] --> PG
    BE[BE IndexerClient] -->|GET /api| API
```

## Contracts được subscribe

13 contracts + 1 factory (outcome token):

| Contract | Source file |
|---|---|
| Diamond MarketFacet | `MarketFacet.ts` |
| Diamond EventFacet | `EventFacet.ts` |
| Diamond AccessControlFacet | `AccessControlFacet.ts` |
| Diamond DiamondCutFacet | `DiamondCutFacet.ts` |
| Diamond PausableFacet | `PausableFacet.ts` |
| PrediXHookV2 | `PrediXHookV2.ts` |
| PrediXHookProxyV2 | `PrediXHookProxyV2.ts` |
| PrediXExchange | `PrediXExchange.ts` |
| PrediXRouter | `PrediXRouter.ts` |
| ManualOracle | `ManualOracle.ts` |
| ChainlinkOracle | `ChainlinkOracle.ts` |
| Uniswap PoolManager | `PoolManager.ts` (Initialize, Swap, ModifyLiquidity — pricing layer) |
| **OutcomeToken factory** | `OutcomeToken.ts` — auto-subscribe mỗi khi `MarketCreated` emit, từ block đó forward |

Source: `INDEXER/src/`.

## Quan trọng: Router.Trade là canonical source

Indexer dùng **`Router.Trade`** là nguồn duy nhất để tăng `protocolStats.totalTrades` và `market.volume`.

- Không double-count với `Hook_MarketTraded` (emit trong mỗi AMM swap — có thể từ Router)
- Không double-count với `Exchange.OrderMatched` (CLOB internal matching)

Đây là fix của audit bug V1 dual-count.

## Env vars

```bash
CHAIN_ID=1301
START_BLOCK=<deploy_block>
DIAMOND_ADDRESS=0x...
HOOK_PROXY_ADDRESS=0x...
EXCHANGE_ADDRESS=0x...
ROUTER_ADDRESS=0x...
MANUAL_ORACLE_ADDRESS=0x...
CHAINLINK_ORACLE_ADDRESS=0x...
POOL_MANAGER_ADDRESS=0x...

PONDER_RPC_URL_PRIMARY=https://sepolia.unichain.org
PONDER_RPC_URL_BACKUP=<fallback RPC>
PONDER_RPC_URL_BACKUP2=<another fallback>

DATABASE_URL=postgresql://user:pass@localhost:5433/predix
```

Ponder require ít nhất 1 RPC URL; sẽ `throw` at boot nếu không có.

## Dev commands

```bash
pnpm install
pnpm codegen         # Regenerate types từ schema
pnpm dev             # Start indexer + API server (port 42069)
pnpm test            # Vitest suite (27 tests)

# Script
scripts/dev.sh          # Dev với log visible
scripts/wipe-db.sh      # Wipe DB cho re-backfill
scripts/dev-reset.sh    # Wipe + boot + health poll
```

## Performance

- Backfill từ block 0 → block hiện tại: ~30 phút trên 4 vCPU (testnet)
- Live indexing: lag < 15s trong điều kiện bình thường
- Polling interval: 1s (per `ponder.config.ts`)

## Liên quan

- [Schema (28 Tables)](01-schema.md)
- [REST API](02-rest-api.md)
- [Events Mapping (SC → Table)](03-events-mapping.md)
- [Reorg & Finality](04-reorg-finality.md)
