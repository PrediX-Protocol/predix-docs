# Resolution & oracle

A market needs a source of _truth_ to determine whether YES or NO wins. That source is the **oracle**.

## Market Lifecycle

![Market lifecycle states: Created → Trading → EndTime → Resolved → Redeemable. Alternative: EndTime → RefundMode → Refunded](../.gitbook/assets/04-market-lifecycle.svg)

| Stage          | Description                                                                     |
| -------------- | ------------------------------------------------------------------------------- |
| **Created**    | Creator (with `CREATOR_ROLE`) creates a market, sets `endTime` + selects oracle |
| **Trading**    | Users split / merge / trade until `endTime`                                     |
| **EndTime**    | Trading closes (hook blocks add liquidity + swap). Oracle window opens          |
| **Resolved**   | Oracle calls `resolveMarket()` with the outcome                                 |
| **Redemption** | Users holding the winning token → redeem for USDC                               |
| **RefundMode** | Fallback if oracle cannot resolve (oracle down, dispute hung)                   |
| **Refunded**   | Users burn YES+NO pairs → receive USDC pro-rata                                 |

## 4 Oracle Types

![4 oracle types: ChainlinkOracle (price, auto), ManualOracle (subjective, multisig 2/3), UMAOracle (decentralized, 48h dispute, Phase 2), Committee (t-of-N, Phase 3)](../.gitbook/assets/36-oracle-types.svg)

### ChainlinkOracle

Automatic, permissionless.

![ChainlinkOracle: creator register(feed, threshold, snapshotAt) → endTime passes → anyone resolve(roundIdHint) → verify adjacency + sequencer uptime → outcome = price ≥ threshold](../.gitbook/assets/37-chainlink-oracle-flow.svg)

**Use case**: Price-threshold markets (BTC, ETH, asset prices, FX rates).

### ManualOracle

Multisig 2/3 reads the result from an off-chain source and signs the transaction.

* Source: Reuters, AP, official APIs, on-chain data, video streaming.
* Outcome is **immutable** once set (invariant INV-6).
* Audit trail: `OracleReportCreated` event on-chain.

**Use case**: Subjective events (sports, elections, debate outcomes).

**Risk mitigation**:

* HSM signer + role separation
* Public audit trail per signature
* Refund mode escape hatch if an error is discovered after the fact

### UMAOracle (Phase 2 — TBA)

Permissionless propose + 48h dispute window.

![UMAOracle: proposer propose(outcome, bond) → 48h dispute window → no dispute: finalize + refund bond; dispute: DVM vote → loser loses bond → market resolved](../.gitbook/assets/38-uma-oracle-flow.svg)

**Bond sizing**: `max(min_bond, min(market_tvl x 0.5%, max_bond))`. Range $500 - $50,000 USDC.

**Use case**: Events requiring decentralized resolution without multisig dependency.

### Committee Oracle (Phase 3 — TBA)

* t-of-N threshold signature (e.g., 5-of-9 validators).
* Commit-reveal voting prevents front-running.
* PRX slashing if a vote contradicts the final consensus.
* Cross-chain via Wormhole / LayerZero.

**Use case**: Cross-chain governance outcomes, complex composite events.

## Comparison

|                   | Manual                | Chainlink                  | UMA                           | Committee                 |
| ----------------- | --------------------- | -------------------------- | ----------------------------- | ------------------------- |
| Who resolves      | Multisig 2/3          | Anyone                     | Anyone proposes, DVM disputes | t-of-N validators         |
| Subjective events | ✅                     | ❌                          | ✅                             | ✅                         |
| Dispute           | Off-chain social      | None (data is law)         | On-chain 48h                  | On-chain commit-reveal    |
| Latency           | Instant after signing | After 1 round (\~30s-1min) | 48h default                   | After commit-reveal cycle |
| Decentralization  | Low                   | Medium                     | High                          | High                      |

## Refund Mode — Last Resort

When no oracle can resolve → admin enables refund mode via 48h timelock → users burn YES+NO pairs → receive USDC pro-rata.

Flow details: [Oracle — Refund mode](../developers-guide/oracle.md#refund-mode--last-resort) · [Redeem & refund](../users-guide/first-trade/redeem-refund.md).

## Incorrect Resolution — What to Do

| Phase                | Mechanism                                                                                           |
| -------------------- | --------------------------------------------------------------------------------------------------- |
| **Phase 1** (Manual) | Multisig discussion, social consensus. If majority agrees resolution was wrong → enable refund mode |
| **Phase 2** (UMA)    | Dispute via UMA protocol, DVM vote                                                                  |
| **All phases**       | `isResolved=true` is never reverted (INV-6 hard invariant)                                          |

## Who Can Create Markets

* **Phase 1**: Addresses with `CREATOR_ROLE` (admin + whitelisted creators).
* **Phase 3 (TBA)**: Permissionless — anyone can create a market by staking a **PRX bond** (10k PRX proposed). Bond is slashed if the market is malformed or resolution is disputed.

Details: [Create a market](../users-guide/liquidity-and-market/create-market.md).

## Timing

* **Resolve window**: 7 days after endTime. If delayed → may trigger default refund mode.
* **Redemption**: No hard deadline, 365-day grace period, after which admin may call `sweepUnclaimed` to transfer to treasury.
* **UMA dispute**: 48h after proposal.

Time parameters are configurable per-market or globally. See [Smart contracts](../developers-guide/architecture.md).
