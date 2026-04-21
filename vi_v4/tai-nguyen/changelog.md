---
description: Changelog của protocol + docs. Track breaking changes + migration note.
---

# Changelog

Protocol + docs version theo **semver**. Breaking changes bump major; feature bump minor; fix/doc bump patch.

---

## [Unreleased]

### Docs
- v4 restructure — audience segmentation (5 personas: web2 user, dev, VC/grant, auditor, marketing)
- Thêm section Backend API (11 files)
- Thêm section Indexer (5 files)
- Thêm section Security (6 files)
- Thêm section Developer guides (9 files, expanded từ 5)
- Fix drift: Hook V1 → V2, CUT_EXECUTOR_ROLE, EventFacet naming, 3 MatchType, redemption fee snapshot

### Protocol (testnet)
- Ongoing: external audit engagement

---

## [2.0.0] — 2026-04-17 (Testnet Launch)

### Smart Contracts

**Added**
- Diamond Proxy (EIP-2535) với 6 facets: MarketFacet, EventFacet, AccessControlFacet, PausableFacet, DiamondCutFacet, DiamondLoupeFacet
- PrediXHookV2 với ERC1967 proxy + 48h timelock upgrade
- PrediXExchange — CLOB với 3 MatchType (COMPLEMENTARY, MINT, MERGE)
- PrediXRouter — stateless aggregator, Permit2-native
- ManualOracle + ChainlinkOracle adapters
- TransientReentrancyGuard (EIP-1153)
- 7 critical invariants với Foundry invariant fuzz
- 5 roles: DEFAULT_ADMIN_ROLE, ADMIN_ROLE, OPERATOR_ROLE, PAUSER_ROLE, CUT_EXECUTOR_ROLE
- TimelockController với 48h delay cho diamondCut

**Security**
- **FINAL-H04**: Redemption fee snapshot tại createMarket — admin không thể raise fee retroactively
- **FINAL-D-03**: Oracle re-verify tại resolveMarket — revoke compromise oracle prevents resolve
- **NEW-01**: `DEFAULT_ADMIN_ROLE` không thể bypass timelock — `CUT_EXECUTOR_ROLE` chỉ TimelockController giữ
- **H-H03**: Identity commit qua EIP-1153 replace V1 (block+direction) anti-sandwich mechanism

**Changed từ V1**
- Hook V1 → V2: removed `(block.number, direction)` sandwich detection, added identity commit
- Exchange: refactor pending fills race với upsert pattern
- Router: thêm `_refundAndAssertZero` check mọi public function

**Tests**: 743 tests passing (51 shared + 293 diamond + 141 hook + 99 exchange + 83 router + 76 oracle)

### Indexer (Ponder 0.16.6)

**Added**
- 28 tables schema (market, event_group, trade, position, exchangeOrder, priceSnapshot, protocolStats, userStats, audit trails)
- 13 contracts subscribed (Diamond facets, Hook, Exchange, Router, Oracle, PoolManager)
- Auto-subscribe OutcomeToken Transfer events on MarketCreated (factory pattern)
- Hono REST `/api/*` cho BE consumer
- 27 vitest tests passing
- Reorg safety: dùng `finalized` block tag (~12-15 min L2 finality)

**Fixed từ V1**
- **Bug #1**: protocol_stats first-insert — seed EMPTY_STATS_ROW
- **Bug #2**: Exchange pending-fills race — upsert pattern thay in-memory Map
- **Bug #3**: position row missing cho protocol contracts — filter `isProtocolContract()`
- **Dual-count bug**: Router.Trade là canonical source cho volume; Exchange và Hook không ảnh hưởng totalTrades

### Backend (v2 — NestJS + Fastify + zod)

**Added**
- 27 modules: markets, events, trading, users, portfolio, leaderboard, social, gamification, admin, auth, faucet, rewards, notifications, candles, comments, holders, stats, protocol config
- OpenAPI spec auto-generated từ zod schemas (`openapi/v2.json`)
- SIWE (EIP-4361) authentication
- 2-tier cache (hot 2s, warm 60s)
- Response envelope `{ data, meta }` + error `{ error, meta }`
- 60+ endpoints public read + admin write

**Conventions**
- Schema-first zod → DTO → OpenAPI
- Primitives: lowercase address, decimal-string price, unix-sec timestamp, LocalizedString
- Discriminated union cho polymorphic market kinds

### Frontend (Next.js 16 + React 19 + Tailwind 4)

**Added**
- App Router architecture
- `openapi-fetch` typed client từ BE spec
- Adapter layer với 100% coverage gate
- Wagmi 2 + Viem 2 + RainbowKit cho wallet
- ZeroDev passkey (account abstraction)
- Circle BridgeKit cho cross-chain testnet transfer
- i18n: en / vi / ja / ko
- Zustand persist mode (novice/pro trading mode)

### Deployed Addresses (Unichain Sepolia)

Xem [Deployed Addresses](../smart-contracts/deployed-addresses.md).

---

## Roadmap (dự kiến)

### [2.1.0] — Q2 2026 (Post-audit)
- External audit final + remediation
- Mainnet deploy Unichain (Chain ID 130)
- Launch bounty program (Sherlock hoặc Immunefi)
- WebSocket streams cho BE

### [2.2.0] — Q3 2026
- Official SDK release (`@predix/sdk-contracts`, `@predix/sdk-api`, `@predix/sdk-react`)
- Keyset-based pagination cho large list endpoints
- UMA Optimistic Oracle adapter (Phase 2 oracle)

### [3.0.0] — Q4 2026 (indicative)
- Cross-chain oracle (Wormhole / LayerZero)
- Multi-chain hook deploy
- Scalar markets (range outcomes) upgrade

---

## Docs changelog

### 2026-04-21 — v4 restructure
- Audience-segmented SUMMARY với 5 personas
- Fixed drift: Hook V1→V2, 5 roles (include CUT_EXECUTOR), 3 MatchType, EventFacet (not Category), redemption fee snapshot
- Added Backend API section
- Added Indexer section
- Added Security section với 7 invariants
- Added deployed-addresses canonical page
- Added Custom Errors registry
- Added llms.txt cho AI agent consumption
- Expanded Developer Guide từ 5 → 9 files

### 2026-03 (v3)
- Initial public docs với user flow + concept + SC overview
- Vietnamese primary locale

---

## Breaking change policy

- **Protocol breaking**: 90 ngày advance notice + migration guide
- **BE API breaking**: new version prefix (`/api/v3`) + 90 ngày sunset của v2
- **SDK breaking**: bump major + migration doc
- **Docs breaking**: redirect URL cũ + changelog entry

## Subscribe changelog

- Watch GitHub repo: [github.com/predix-protocol](https://github.com/predix-protocol)
- Join Discord `#announcements`: [discord.gg/predixpro](https://discord.gg/predixpro)
- Twitter: [@PrediXPro](https://twitter.com/predixpro)
