# Redeem & refund

After a market resolves, exchange winning tokens for USDC. If a market cannot be resolved, use the refund flow.

### Redeem vs Refund

|                     | 🏆 **Redeem**                   | 🛡️ **Refund**                                                               |
| ------------------- | ------------------------------- | ---------------------------------------------------------------------------- |
| **When**            | Market resolved cleanly         | Market cannot be resolved (oracle down, dispute hung, multisig unresponsive) |
| **Trigger**         | Automatic, oracle posts outcome | Admin enables via 48h timelock                                               |
| **What you submit** | Winning tokens (YES or NO)      | YES + NO **pairs**                                                           |
| **Payout**          | `$1 per winning share`          | `1 USDC per pair burned`                                                     |
| **Losing tokens**   | Worthless (`$0`)                | Single-sided holders need workaround                                         |

***

## Redeem — market has resolved

**Conditions**:

* `market.isResolved == true`
* You hold the winning token (YES if outcome=true, NO if outcome=false)

### Steps

1. [Portfolio](portfolio.md) → filter **Resolved markets**.
2. Each market shows a **Redeem** button if you hold the winning token.
3. Click → preview: token count, USDC to receive.
4. Confirm in your wallet → \~2s transaction complete.
5. USDC arrives in your wallet. Winning tokens are burned; losing tokens = $0.

### Batch redeem

Multiple resolved markets → click **Claim All** → batched via **passkey smart account** (1 click, 1 tx, gas via paymaster). EOA users: each market requires a separate tx (wallets do not support native batching). Both account types receive sponsor coverage if the user qualifies for the program.

![Batch claim: Click Claim All → smart account bundle redeem(m1...mN) → 1 UserOp → Diamond burn tokens + transfer total USDC](../../.gitbook/assets/16-claim-batch.svg)

### Losing tokens

* **No longer have value**.
* Cannot be redeemed or traded (pool is drained).
* They still appear in your wallet with a balance, but market value = $0. Hide them by removing from your watchlist.

## Refund mode — market cannot be resolved

**When**: Oracle is down, dispute is hung, multisig is unresponsive → admin enables refund mode via a 48h timelock.

**Conditions**:

* `market.refundModeActive == true`
* You hold **both YES and NO**

### Steps

1. Portfolio → the market displays a **Refund available** badge.
2. Click **Refund** → preview: `min(yesBalance, noBalance)`, USDC to receive.
3. Confirm → tx \~2s.
4. USDC returns to your wallet. The YES+NO pair is burned.

### Formula

```
refundAmount = min(yesBalance, noBalance)
payout       = refundAmount USDC (1:1)
```

Example: you hold 100 YES + 80 NO:

* Refund 80 pairs → receive 80 USDC.
* 20 YES remaining → **no NO to pair with**, cannot be refunded.

### Note: refund is pair-based only

If you hold only one side (e.g. bought 100 YES via the Router, no NO held), you **cannot claim** USDC through the standard refund flow.

**Workaround**:

* Buy NO from someone still selling (NO price typically drops near $0 when refund mode is active since no one expects a win).
* Pair with your YES to refund.
* Your loss is the NO purchase price.

### This is a design trade-off

Refund mode prioritizes **pro-rata fairness** over first-come-first-serve. This prevents a scenario where early claimers drain all USDC, leaving nothing for later users.

Phase 2 (TBA): A **single-sided refund** with a 50% haircut may be introduced — burn 100 YES → receive 50 USDC. Currently under governance discussion.

## Who decides to enable refund

![Admin refund: admin propose enableRefundMode → TimelockController 48h delay → execute → user burn YES+NO pairs → USDC pro-rata](../../.gitbook/assets/17-admin-refund.svg)

### Troubleshooting

<details>

<summary>Redeem button not appearing for a resolved market</summary>

**Reason:** Either you don't hold the winning side, or the indexer hasn't caught up to the resolution event.

**Fix:**

* Check the market's resolved outcome — do you hold YES (if outcome = true) or NO (if outcome = false)?
* If outcome matches your holdings, wait 30 seconds and refresh
* Force a fresh fetch with `?nocache=1` URL param

</details>

<details>

<summary>"Insufficient balance" on redeem</summary>

**Reason:** Your token balance is lower than the contract expects (possibly due to a recent transfer or pending indexer sync).

**Fix:**

* Verify your token balance on the explorer
* Wait 30 seconds for indexer to sync, then retry
* If balance is genuinely insufficient, you cannot redeem more than you hold

</details>

<details>

<summary>Batch redeem failed mid-way (EOA wallets)</summary>

**Reason:** EOA wallets process each market as a separate tx. If one fails (e.g., gas spike), the remaining will still process — but you may need to retry the failed ones.

**Fix:**

* Refresh Portfolio — successful redemptions are reflected
* For the failed market(s), click **Redeem** individually
* Consider switching to Passkey for atomic batching

</details>

<details>

<summary>Refund button shows "Insufficient pair"</summary>

**Reason:** You hold only one side of the market (e.g., 100 YES, 0 NO) and refund requires pairs.

**Fix:** See Single-sided holders — workaround above. Buy the missing side at near-zero price from active sellers, then refund the pair.

</details>

<details>

<summary>Refund mode active but no sellers for the opposite side</summary>

**Reason:** All holders may also be single-sided (everyone bought the same direction via Router). Liquidity dries up when refund mode is active.

**Fix:**

* Post a limit order on the opposite side at a very low price (`$0.01`)
* Wait for the orderbook to develop — usually sellers appear within 24h
* Discord `#refund-coordination` for matching with counterparties

</details>

<details>

<summary>Resolved market still shows as "Pending" in Portfolio</summary>

**Reason:** Resolution oracle posted the outcome on-chain but indexer is still syncing.

**Fix:**

* Wait 1–2 minutes for indexer sync
* Verify the resolution on-chain via the explorer
* If stuck >10 minutes, report on Discord `#bug-reports`

</details>

***
