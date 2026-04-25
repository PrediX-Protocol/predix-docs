# Changelog

Theo dõi thay đổi của protocol. Date format YYYY-MM-DD.

## 2026-04-25 — Documentation v5

- Rewrite GitBook theo pattern DeFi docs thành công (Uniswap, Aave, Polymarket, GMX, Morpho).
- 36 file, cắt từ 98 của v4 (giảm ~64% files, ~71% words).
- Bỏ section team/roadmap/GTM/legal/fundraising — dời pitch deck riêng.
- Thêm persona-based navigation (User / Staker / Developer).
- Mermaid diagrams cho architecture, lifecycle, trade flow, fee distribution.
- Update contract addresses (fresh deploy 2026-04-20).

## 2026-04-22 — Paymaster mainnet-ready

- Deploy `PrediXPaymaster` testnet (`0x1637a7eB463b1b12906feF71eF23B76181340Cb7`).
- Support Kernel v3 smart account + paymaster-sponsored UserOp.
- FE integrate passkey sign-up + ECDSA-upgraded smart account.

## 2026-04-20 — Fresh testnet deploy

- Re-deploy toàn bộ core contracts trên Unichain Sepolia.
- Diamond: `0x7689E9bf4b2107E2Fd0f1DDA940E2f1143434E39`.
- Router: `0x6698253F38F4A4bbBC4A223309B4E560d83D7ee0`.
- Exchange: `0xE425698e1835DA0A6086eEB85137A36275993F41`.
- Hook proxy: `0x89830AC92Ff936f39C2D11D1fd821c6f977fAAE0`.
- Deploy block: `49799033`.
- Supersedes deploy Apr-17 (Phase 7) và Apr-15 (initial).

## 2026-04-17 — Audit fix batch A

13 commits audit fix chưa trên develop branch (branch `bundle-a/group-x/x2-gap-c-matchmath-helper`):

- **GAP-C MatchMath helper** — unify fill-delta math, preview/execute 1-wei parity.
- **BACKLOG MERGE improvement** — taker MERGE nhận price improvement.
- **NEW-M7 two-pass virtual-NO** — thin pool, safety margin 3%.
- **H-R1 ClobSkipped** — emit event + extract 4-byte selector khi CLOB revert.
- **FINAL-M06 + SPEC-04/05 hook proxy** — 48h self-gated monotonic timelock.
- **NEW-M4 canonical PoolKey** — `registerMarketPool` enforce lpFee + tickSpacing.
- **F-X-02 setDiamond rotation** — replace setDiamond → 48h propose/execute.
- **SPEC-03 CREATOR_ROLE** — gate createMarket + createEvent.
- **FIN-02 Chainlink adjacency** — enforce `updatedAt >= snapshotAt`, prev < snapshotAt.
- **FIN-03 proposeTrustedRouter** — reject silent timer reset.
- **M-NEW-01 beforeDonate endTime** — chặn donate sau endTime.
- **docs SPEC_VS_CODE_MATRIX** — seed matrix SPEC-01 hook permissions.
- **chore gitignore** — exclude planning folder.

## Open blockers (pre-mainnet)

5 finding đang xử lý:

- **C-01 refund asymmetric burn** — fix `min(yes, no)` thay `(yes+no)/2`.
- **H-02 diamondCut timelock** — wrap vào TimelockController 48h.
- **H-03 sweepUnclaimed** — burn outstanding supply trước sweep.
- **H-04 retroactive redemption fee** — snapshot tại creation (đã partial fix).
- **H-05 emergencyResolve** — check oracle state trước khi cho front-run.

Mainnet deploy chờ 5 blocker này resolved + external audit green-light.

## 2026-03-XX — Initial build

- 6 package Solidity monorepo: shared, oracle, diamond, hook, exchange, router.
- Diamond với 6 facet.
- Hook V1 với basic permission.
- Exchange CLOB với 99-tick bitmap + 3 match type.
- Router stateless với Permit2.
- Internal audit V1 (~50 finding, remediated).

## Planned (roadmap)

### Q2 2026 — Mainnet launch
- External audit round 1–2.
- Deploy mainnet Unichain (chain 130).
- Seed LP pool cho 50+ market launch-day.

### Q3 2026 — UMA oracle integration
- Deploy `UMAOracle` adapter.
- Bond mechanism 0.5% TVL scale.
- Phase 2 permissionless propose + dispute.

### Q4 2026 — TGE
- Mint 1B PRX.
- Public TGE sau đạt PMF gate ($500k vol/tháng + 1000 trader unique + 10 market).
- Staking vault + gauge voting live.
- Listing Tier-2 DEX + CEX.

### 2027 — Phase 3
- Permissionless market creation với PRX bond.
- Committee oracle (threshold sig + commit-reveal).
- Cross-chain support (Wormhole / LayerZero).
- Social recovery cho passkey smart account.

---

Cập nhật mỗi release. Subscribe Discord / Twitter (xem [links](links.md)) để theo dõi realtime.
