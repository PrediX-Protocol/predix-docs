---
description: Reorg handling — Ponder dùng Unichain finalized block tag (~12-15 min L2 finality).
---

# Reorg & Finality

## Finality model

Ponder 0.16 sync engine dùng **`finalized` block tag** của chain để decide khi event "safe". Với Unichain (OP Stack L2):

- Finality = L1-anchored (~12-15 phút sau khi block mined)
- Block số finalized được RPC trả qua `eth_getBlockByNumber("finalized")`
- Handler chỉ commit state khi block được finalized

**Trade-off**: lag ~12-15 phút giữa real-time và "indexed state".

{% hint style="info" %}
Ponder 0.16.6 **không expose** `confirmedBlockThreshold` hoặc `finalityBlockCount` option trong ChainConfig. Dùng `finalized` tag là default cứng. Nếu cần tune, phải chờ Ponder upgrade.
{% endhint %}

## Cái gì xảy ra khi reorg?

### Pre-finality reorg

Nếu reorg xảy ra ở block chưa finalized (ví dụ chain canh chỉnh ở depth=3 blocks), Ponder:

1. Detect reorg qua block hash mismatch
2. **Rewind** handler state về block common ancestor
3. Re-process events từ new canonical chain

State DB được "reverted" cho các row affected. Event audit table vẫn full integrity vì các row chỉ commit khi finalized.

### Post-finality

Không có reorg post-finality (per definition của `finalized`). Nếu chain deep reorg xảy ra (rare), Ponder sẽ error và require manual intervention (restart với clean DB).

## Impact cho consumer

### BE

BE fetch data qua Indexer REST. BE không aware của finality — chỉ cache response. Nếu Indexer rewind + re-serve, BE cache invalidate tự nhiên theo TTL 2s/60s.

### FE

FE nhìn thấy state "stale" tối đa ~15 phút + BE cache TTL. Cho hầu hết use case (list markets, portfolio) — acceptable.

Cho critical path (verify trade đã thực thi):

1. FE submit tx
2. Wait receipt (`tx.wait()`)
3. Query **on-chain** (không qua Indexer) để confirm state mới
4. Indexer sẽ catch up sau ~15 phút

### Bot / MM

Bot cần fill monitoring nên subscribe **on-chain events** qua viem `watchContractEvent` thay vì poll Indexer. Indexer lag ~15 phút không phù hợp cho market making.

## Dev / self-host ops

### Start fresh (re-backfill from block 0)

```bash
scripts/wipe-db.sh
pnpm codegen
pnpm dev
```

Ponder re-index từ `START_BLOCK`. Backfill testnet ~30 phút trên 4 vCPU.

### Recover after deep reorg error

Rare, nhưng nếu Ponder throw "deep reorg detected":

1. Stop Ponder
2. Wipe DB
3. Check RPC provider — có thể RPC trả block cũ
4. Switch `PONDER_RPC_URL_PRIMARY` sang provider khác
5. Restart

### Monitoring

Prometheus metrics (`/metrics`):

- `ponder_sync_block_number` — current indexed block
- `ponder_sync_lag_seconds` — delta so với `latest` block
- `ponder_reorg_detected_total` — count reorg events

Alert threshold:

- `sync_lag_seconds > 60` sustained 5 phút → RPC slow hoặc handler bottleneck
- `reorg_detected_total` tăng > 2/hour → RPC unstable

## Ponder version note

- **Current**: Ponder 0.16.6
- Ponder 0.17+ có plan expose user-tunable `confirmedBlockThreshold` — khi upgrade sẽ document override option
- Ponder version bump = schema re-validation + re-backfill risk → test trên staging trước

## Alternative finality strategies (out of scope hiện tại)

1. **Optimistic indexing**: Commit state ngay, rollback on reorg (higher UX, higher complexity)
2. **Multi-RPC consensus**: Require N RPCs agree (higher reliability, higher cost)
3. **Shadow fork indexer**: Run 2 instances, compare — catch divergence

Chưa implement — current setup đủ cho testnet + early mainnet.
