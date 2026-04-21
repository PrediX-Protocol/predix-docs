---
description: Emergency response procedure khi critical incident.
---

# Incident Playbook

Mục tiêu: minimize user harm trong critical incident. Team có clear authority + clear steps.

## Severity triage

| Severity | Ví dụ | Max response time |
|---|---|---|
| **P0 — Critical** | Active drain exploit; bug invariant INV-1/3 | < 15 phút — pause immediate |
| **P1 — High** | Oracle feeding wrong data; single-market issue | < 1 giờ — market-level response |
| **P2 — Medium** | API down; UI bug preventing trade | < 4 giờ — comm + workaround |
| **P3 — Low** | Minor UX, delayed indexing | < 24 giờ — backlog |

## P0 response flow

```mermaid
flowchart TB
    Detect[Detection<br/>• monitoring alert<br/>• bug report<br/>• anomaly] --> Assess{Assess}
    Assess -->|Active exploit| Pause[PAUSER_ROLE:<br/>pause(MARKET)]
    Assess -->|Bug found, not yet exploited| Patch
    Pause --> Assess2[Root cause analysis]
    Patch[Prepare patch<br/>via timelock or new impl]
    Assess2 --> Patch
    Patch --> Deploy[Deploy fix<br/>testnet verify]
    Deploy --> Resume[unpause MARKET<br/>or complete upgrade]
    Resume --> PM[Post-mortem + comm]
```

## P0 playbook — active exploit

### Immediate actions (< 15 phút)

**Person 1 (any on-call)**:

```solidity
// PAUSER_ROLE calls via Diamond
diamond.pause(Modules.MARKET);
```

Module MARKET pause stops:
- `splitPosition`, `mergePositions`
- `redeem`, `redeemMarketTokens`
- `placeOrder` trên Exchange
- Trading qua Router (AMM + CLOB taker path)

Module MARKET pause KHÔNG stop:
- `refund` — user còn exit được
- `resolveMarket` — finality vẫn process
- `emergencyResolve`, `enableRefundMode`, `sweepUnclaimed`
- `cancelOrder` — user rút USDC về được

**Person 2 (security lead)**:

- Post incident notice lên Discord #announcements + Twitter
- Template:
  > "🚨 Security issue detected. Trading paused preventatively while we investigate. User funds are safe. Updates every 30 phút. Details: <link>"

**Person 3 (ops)**:

- Snapshot relevant on-chain state via `cast` hoặc explorer
- Export RPC logs + Indexer handler logs
- Start public incident doc (Google Doc hoặc GitHub Issue)

### Within 1 hour

1. **Reproduce** bug locally với fork test
2. **Scope assessment** — markets/users affected
3. **Draft remediation** — facet upgrade hoặc config change
4. **Community update** — "We've identified the issue. Remediation path: X. ETA: Y."

### Within 24 hours

1. **Deploy fix** qua timelock 48h — OR nếu critical và scope-narrow, team-approved emergency deploy procedure
2. **Verify fix** trên testnet + fork mainnet
3. **Schedule unpause** với community notice

### Post-incident (within 7 days)

1. **Public post-mortem** — [blog.predixpro.io/incidents/<date>](https://blog.predixpro.io)
2. Include:
   - Timeline
   - Root cause
   - Immediate fix
   - Long-term prevention
   - Compensation plan (nếu users lost funds)
3. **Update docs** (including this playbook)
4. **Reporter credit** (bug bounty hall of fame)

## P1 playbook — oracle wrong data

Nếu một oracle report kết quả sai (vd: manual oracle admin operator error, hoặc Chainlink feed corrupt):

### Response

**OPERATOR_ROLE action**:

```solidity
// Nếu oracle compromise / wrong data → revoke approval + refund mode
diamond.setApprovedOracle(badOracle, false);  // ADMIN role
diamond.enableRefundMode(marketId);            // OPERATOR role
```

Flow:

1. Admin revoke oracle approval → market không thể resolve với oracle đó (FINAL-D-03 re-verify)
2. Operator enable refund mode → holders burn token → USDC 1:1
3. User `refund(marketId, amount)` → recover fund
4. Separate comm explain: "Oracle X mis-reported. We've refunded market Y. No impact on other markets."

### Prevent recurrence

- Review oracle approval process
- Add dispute window cho ManualOracle (timelock trước accept)
- Diversify oracle sources cho high-value markets

## P1 playbook — single market issue

Ví dụ: market title typo, duplicate market, wrong `endTime`.

### Response options

**Option A — Admin pause market-specific** (soft-hide qua Mongo overlay):

```typescript
POST /api/v2/admin/markets/:id/hide
```

Market không xuất hiện trên UI. Existing holders vẫn trade/redeem on-chain (BE hide chỉ là UI layer).

**Option B — Refund mode** (hard):

```solidity
diamond.enableRefundMode(marketId);
```

User `refund` → USDC back. Market "effectively cancelled".

**Option C — Wait + resolve** (nếu issue không affect trading):

Market endTime đến → resolve như bình thường. Post-resolve comm explain UX issue.

## P2 playbook — infrastructure down

BE / Indexer / RPC outage.

### Response

1. **FE fallback**: FE detect BE 5xx → show "Backend temporarily unavailable" banner. User vẫn có thể trade qua wallet (direct on-chain) nếu advanced.
2. **Indexer re-sync**: Nếu Indexer lag → investigate RPC, scale up, switch RPC provider
3. **BE restart**: Standard k8s rolling restart
4. **Cache invalidation**: Flush lru-cache nếu stale data persist

User funds không bị ảnh hưởng — on-chain state luôn intact.

## Pause criteria (matrix)

| Scenario | Pause MARKET? | Pause DIAMOND? | Enable refund? |
|---|---|---|---|
| Active drain exploit (INV-1 violation detected) | ✅ | ✅ maybe | Per-market |
| Oracle compromise on 1 market | ❌ | ❌ | ✅ that market |
| Oracle compromise on N markets (systemic) | ✅ | ❌ | Per-market |
| Exchange bug (MatchType incorrect) | ✅ | ❌ | Depends |
| Hook bug (sandwich bypass) | ✅ | ❌ | Investigate first |
| Router bug (non-custody violation) | ✅ | ✅ | If funds stuck |
| UI / BE / Indexer down | ❌ | ❌ | ❌ — user can use wallet direct |

## Communication templates

### Initial notice (T+0)

> 🚨 **[INCIDENT DETECTED]** We've detected an issue affecting \[scope]. As a precaution, we've paused \[module/market]. User funds are safe. Investigating — updates every 30 min.
> Tracking: https://status.predixpro.io/<incident-id>

### Progress update

> **\[UPDATE T+30min]** Root cause identified: <1 sentence summary>. Remediation path: <1 sentence>. ETA: <time>. Users \[N] affected — no fund loss confirmed.

### Resolved

> ✅ **\[RESOLVED]** Issue resolved. Module \[X] re-enabled. Post-mortem in 7 days. Thank you for patience. If you believe you were affected, contact support@predixpro.io.

## Test regularly

- Tabletop exercise: 1x / quý
- Chaos drill: simulate RPC down, DB corrupt, pause-unpause cycle trên staging

## Contacts (internal)

- **On-call rotation**: xem internal wiki
- **Team TG group**: critical incidents
- **Escalation**: Security lead → CTO → CEO (nếu > 4 hours no resolution)
