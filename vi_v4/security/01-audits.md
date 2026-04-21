---
description: Audit trail — internal review + external audit roadmap.
---

# Audit Reports

{% hint style="warning" %}
**External audit đang trong progress**. Mainnet launch sẽ sau khi external audit hoàn tất + remediate finding. Đây là trang canonical cho audit trail.
{% endhint %}

## Internal review (completed)

Team đã hoàn thành internal audit trên codebase testnet-deployed. Kết quả:

### V1 (initial)

- **File**: `SC/audits/AUDIT_REPORT_v1.md`
- **Scope**: Toàn bộ 6 packages
- **Date**: Phase 3-4 (~2026-03)
- **Findings**: Mix of informational + low + high. Full list trong report.

### V2 (post-refactor)

- **File**: `SC/audits/FULL_AUDIT_V2_20260417.md`
- **Scope**: Re-audit sau refactor hook V1→V2 + diamond cut timelock
- **Date**: 2026-04-17
- **Status**: Majority issues remediated pre-deploy

### Consolidated

- **File**: `SC/audits/CONSOLIDATED_AUDIT_REPORT.md`
- Combined view của tất cả finding với remediation status.

### Verification reports

- `VERIFY_FULL_AUDIT_V2_20260417.md` — verify FULL_AUDIT_V2 remediations pass
- `VERIFY_SC_POST_DEPLOY_20260417.md` — verify deployed contracts khớp source
- `VERIFY_SC_PHASE7_COMPREHENSIVE_SMOKE_20260417.md` — smoke test toàn protocol
- `VERIFY_SC_PHASE7_E2E_20260417.md` — end-to-end flow test

## Key findings remediated

### H01 / FINAL-H04 — Redemption fee snapshot

**Issue**: `defaultRedemptionFeeBps` là mutable live value → admin có thể raise fee ngay trước khi user redeem.

**Fix**: Snapshot `defaultRedemptionFeeBps` vào `MarketData.snapshottedRedemptionFeeBps` tại `createMarket`. Admin subsequent updates không affect existing markets.

**Location**: `SC/packages/diamond/src/facets/market/MarketFacet.sol`

### FINAL-D-03 — Oracle re-verify at resolve

**Issue**: Nếu oracle bị compromise sau `createMarket`, admin revoke approval không ngăn được market resolve với oracle cũ.

**Fix**: `resolveMarket` re-check `LibConfigStorage.approvedOracles[oracle]` tại time of resolve. Revoked → revert → admin có thể trigger `enableRefundMode` thay thế.

### NEW-01 — Diamond cut authority separation

**Issue**: `DEFAULT_ADMIN_ROLE` có thể bypass timelock upgrade bằng cách grant `CUT_EXECUTOR_ROLE` cho address non-timelock.

**Fix**: Deployment procedure enforce `CUT_EXECUTOR_ROLE` chỉ grant cho `TimelockController`, sau đó `revoke` từ mọi address khác. Verification: `getRoleMemberCount(CUT_EXECUTOR_ROLE) == 1` and member = TimelockController.

### H-H03 — Hook identity commit

**Issue**: V1 Hook anti-sandwich dùng `(block.number, direction)` tracking — có false positive (cùng block, cùng direction 2 user khác nhau) và false negative (cross-block sandwich).

**Fix**: V2 Hook dùng **identity commit qua EIP-1153 transient storage**. Router commit `msg.sender` trước swap; Hook verify commit present trong `beforeSwap`. Sandwich attacker không nằm trong TrustedRouter whitelist → không commit được → `beforeSwap` revert.

### Exchange bug #2 — Pending fills race

**Issue**: V1 dùng in-memory Map để track pending fills → race condition cross-tx.

**Fix**: Refactor sang on-chain upsert pattern. State chỉ trong EVM storage, không off-chain.

### V1 dual-count bug

**Issue**: Indexer count volume từ cả `Router.Trade` và `Hook_MarketTraded` → double-count khi trade đi qua AMM via Router.

**Fix**: Indexer chỉ dùng `Router.Trade` làm canonical. `Hook_MarketTraded` chỉ emit cho priceSnapshot analytics, không ảnh hưởng volume/tradeCount.

## External audit (in progress)

### Engagement scope

| Item | Status |
|---|---|
| Audit firm selection | In progress (shortlist: Spearbit, Trail of Bits, OpenZeppelin, Zellic) |
| Audit spec document | `SC/specs/AUDIT_SPEC.md` |
| Scope | 6 packages: shared, oracle, diamond, hook, exchange, router |
| Methodology | Manual review + automated tools (Slither, Mythril) + invariant testing (Foundry) |
| Report delivery | TBA — dự kiến Q2 2026 |

### Disclosure timeline

Sau khi external audit hoàn tất:

1. **T+0**: Audit firm deliver draft report
2. **T+7d**: Team review + remediate critical findings
3. **T+14d**: Firm re-review remediation
4. **T+21d**: Final report published (PDF) — link từ trang này
5. **T+30d**: Mainnet deploy pending final green light

## Audit history table

| Auditor | Date | Scope | Report |
|---|---|---|---|
| Internal Team | 2026-03 | Initial code | `AUDIT_REPORT_v1.md` |
| Internal Team | 2026-04-17 | Post-refactor | `FULL_AUDIT_V2_20260417.md` |
| **External (TBD)** | **Q2 2026** | Full 6 packages | **TBA** |

## Test coverage summary

| Package | Tests | Coverage highlights |
|---|---|---|
| `shared` | 51 | Reentrancy guard fuzz, outcome token ERC20/Permit |
| `diamond` | 293 | Lifecycle end-to-end, event mutual exclusion, access control, timelock |
| `hook` | 141 | Dynamic fee, identity commit, proxy upgrade |
| `exchange` | 99 | 3 MatchType, solvency invariant, pause asymmetry |
| `router` | 83 | Zero-custody invariant, split routing, Permit2, quote-revert |
| `oracle` | 76 | Staleness, sequencer uptime, round completeness |
| **Total** | **743** | |

**7 critical invariants** (xem [Invariants](02-invariants.md)) — all covered bởi property tests.

## Formal verification

Chưa có. Candidate cho future:

- Certora cho Diamond storage integrity
- Halmos cho router zero-custody invariant
- Foundry invariant testing (đã có — 7 invariants pass)
