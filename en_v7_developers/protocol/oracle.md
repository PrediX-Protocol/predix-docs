# Oracle

The oracle is the layer that reports real-world event outcomes on-chain. PrediX uses a pluggable architecture — multiple oracle types coexist.

## The core problem

Smart contracts cannot autonomously determine:

* "Has Argentina won the FIFA World Cup?"
* "Has BTC crossed $100k?"
* "Who won the 2028 US election?"

An **external source** must deliver data on-chain. If the oracle is wrong, the market resolves incorrectly and users lose funds.

## 4 phases, 4 oracle types

![4 oracle types: ChainlinkOracle (price, auto), ManualOracle (subjective, REPORTER role + optional challenge window), UMAOracle (decentralized, 48h dispute, Phase 2), Committee (t-of-N, Phase 3)](../.gitbook/assets/25-oracle-types.svg)

## ChainlinkOracle

Automated, permissionless.

![ChainlinkOracle: creator register(feed, threshold, snapshotAt) -> endTime passes -> anyone resolve(roundIdHint, prevRoundIdHint) -> verify adjacency + sequencer uptime -> outcome = price >= threshold](../.gitbook/assets/37-chainlink-oracle-flow.svg)

**Use case**: Price-threshold markets (BTC, ETH, asset prices, FX rates).

**Strict checks**:

* Round adjacency — the resolver supplies `(roundIdHint, prevRoundIdHint)`; the oracle asserts the hinted round is the unique one whose `updatedAt >= snapshotAt` and whose predecessor's `updatedAt < snapshotAt`. Stale and skipped rounds revert.
* L2 sequencer uptime — when a sequencer uptime feed is configured, `resolve` reverts if the sequencer is currently down OR has been up for less than `SEQUENCER_GRACE_PERIOD` (1 hour). Avoids resolving against stale data immediately after a sequencer recovery.

## ManualOracle

Role-gated `REPORTER` publishes the outcome from an off-chain source. Beta uses a single hot wallet for fast resolution; production may rotate the role to a multisig.

### Acceptable sources

| Event type   | Acceptable source                            |
| ------------ | -------------------------------------------- |
| Sports       | Official league API, Reuters, AP             |
| Election     | Official election commission, AP, Reuters    |
| Crypto event | On-chain data, official project announcement |
| Weather      | NOAA, regional met office                    |
| Award show   | Official website                             |

### Flow

![ManualOracle: real-world event -> REPORTER verifies >= 2 sources -> report(marketId, outcome) -> OutcomeReported event -> optional challenge window elapses -> anyone resolveMarket -> MarketResolved](../.gitbook/assets/56-manual-oracle-flow.svg)

A reported outcome becomes consumable only after `finalizesAt = reportedAt + challengeDelay`. The `challengeDelay` is admin-controlled, defaults to `0` (immediate resolution), and is capped at 7 days.

### Risk mitigation

The REPORTER role can be rotated to a hardware wallet, KMS-backed signer, or multisig at any time without redeploying the oracle. Each report emits an on-chain audit trail (`OutcomeReported`). Refund mode serves as an escape hatch if the oracle is compromised. Long-term: phase out manual in favor of UMA + committee oracle.

### Revoke and reopen

Admin operations on a pending report (before `finalizesAt`):

* `revoke(marketId)` — tombstones the slot: clears the answer AND freezes it so the reporter cannot re-publish. Admin playbook is revoke → `enableRefundMode` for the affected market.
* `reopenReport(marketId)` — clears the slot WITHOUT freezing, letting the reporter publish a corrected outcome in a fresh challenge window. Only callable while the challenge window is still open.

**Warning**: once `isResolved` flips to `true` (challenge window closed and the outcome was consumed), neither path is available — the resolution is immutable (hard invariant INV-6).

## UMAOracle (Phase 2 — TBA)

Permissionless propose + 48h dispute window.

![UMAOracle: proposer propose(outcome, bond) -> 48h dispute window -> no dispute: finalize + refund bond; dispute: DVM vote -> loser loses bond -> market resolved](../.gitbook/assets/38-uma-oracle-flow.svg)

### Bond sizing

```
bond = max(min_bond, min(market_tvl × 0.5%, max_bond))
min_bond = $500 USDC
max_bond = $50,000 USDC
```

Bond scales with market size -> disincentivizes spam, aligns incentives.

### Use case

Events requiring decentralized resolution without reliance on a multisig.

## Committee oracle (Phase 3 — TBA)

* **t-of-N threshold signature** (e.g. 5-of-9 validators).
* **Commit-reveal voting** prevents front-running.
* **Slashing** of PRX for validators who vote against the final consensus.
* **Stake PRX** to serve as a validator.
* **Cross-chain** support via Wormhole / LayerZero.

### Use case

Cross-chain governance outcomes, complex composite events.

## Oracle type comparison

|                   | Manual                          | Chainlink          | UMA                           | Committee                 |
| ----------------- | ------------------------------- | ------------------ | ----------------------------- | ------------------------- |
| Who resolves      | REPORTER role (EOA or multisig) | Anyone             | Anyone proposes, DVM disputes | t-of-N validators         |
| Subjective events | Yes                             | No                 | Yes                           | Yes                       |
| Dispute mechanism | Optional challenge window       | None (data is law) | On-chain 48h                  | On-chain commit-reveal    |
| Latency           | Immediate or up to 7d window    | \~30s (1 round)    | 48h default                   | After commit-reveal cycle |
| Decentralization  | Low                             | Medium             | High                          | High                      |
| Bond required     | No                              | No                 | Yes                           | Validator stake           |

## Refund mode — last resort

When no oracle can resolve the market:

![Refund mode: oracle fails -> admin proposes enableRefundMode -> 48h timelock -> refundModeActive=true -> user burns min(YES,NO) pairs -> USDC pro-rata](../.gitbook/assets/39-refund-mode.svg)

Details: [Redeem & refund](../../users-guide/redeem-refund.md).

## Incorrect resolution — handling flow

| Phase              | Mechanism                                                                                          |
| ------------------ | -------------------------------------------------------------------------------------------------- |
| **Phase 1 Manual** | Admin `revoke` / `reopenReport` during challenge window; otherwise enable refund mode if confirmed incorrect |
| **Phase 2 UMA**    | Dispute via UMA, DVM is final                                                                      |
| **All phases**     | `isResolved=true` is never reverted (INV-6 hard invariant)                                         |

## Oracle approval list

Diamond maintains a set of approved oracles:

* `approveOracle(addr)` — admin adds a new adapter (instant).
* `revokeOracle(addr)` — admin removes (instant) — only prevents use for **new** markets.
* Markets already created with that oracle **continue to use it** — avoids retroactive breakage.

## Oracle selection per market

| Event type                     | Recommendation                                 |
| ------------------------------ | ---------------------------------------------- |
| Price threshold (BTC > $100k)  | ChainlinkOracle                                |
| Sports / election              | ManualOracle (Phase 1) -> UMAOracle (Phase 2+) |
| On-chain event (gov vote, TVL) | Custom adapter via Diamond approve             |
| Subjective (debate winner)     | UMAOracle                                      |
| Complex composite              | Custom adapter or manual + committee           |

Market creation details: [Create market](../../users-guide/liquidity-and-market/create-market.md).
