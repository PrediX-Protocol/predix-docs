---
description: >-
  Claim your winnings or get a refund on PrediX. Redeem winning tokens for $1
  each, or burn YES+NO pairs to reclaim your USDC on unresolved markets.
---

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

**Conditions:**

* `market.isResolved == true`
* You hold the winning token (YES if outcome=true, NO if outcome=false)

#### <mark style="color:orange;">How to Redeem Your Winnings</mark>

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Locate Finished Markets</mark>

* Navigate to your [Portfolio](portfolio.md).
* Use the filters to select Resolved markets. This will show you all events that have officially concluded.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Check for Eligibility</mark>

* Look for the Redeem button next to each market.

{% hint style="info" %}
Note: This button will only appear if you hold the winning token (the side that correctly predicted the outcome).
{% endhint %}
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Review the Preview</mark>

Click the Redeem button to open a preview window showing:

* Token Count: The total number of winning tokens you are turning in.
* USDC to Receive: The total amount of USDC that will be credited to your account.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 4: Confirm in Wallet</mark>

* Click Confirm and authorize the transaction in your wallet (e.g., biometric authentication for passkeys or a signature for EOA).
* The transaction typically completes in \~2 seconds on Unichain.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 5: Settlement Complete</mark>

* The USDC is deposited directly into your wallet balance.
* Winning tokens are burned (removed from circulation), while losing tokens automatically drop to a value of $0.
{% endstep %}
{% endstepper %}

### <mark style="color:orange;">Batch redeem</mark>

Multiple resolved markets → click **Claim All** → batched via **passkey smart account** (1 click, 1 tx, gas via paymaster). EOA users: each market requires a separate tx (wallets do not support native batching). Both account types receive sponsor coverage if the user qualifies for the program.

![Batch claim: Click Claim All → smart account bundle redeem(m1...mN) → 1 UserOp → Diamond burn tokens + transfer total USDC](../.gitbook/assets/16-claim-batch.svg)

### <mark style="color:orange;">Losing tokens</mark>

* **No longer have value**.
* Cannot be redeemed or traded (pool is drained).
* They still appear in your wallet with a balance, but market value = $0. Hide them by removing from your watchlist.

## Refund mode — market cannot be resolved

**When**: Oracle is down, dispute is hung, multisig is unresponsive → admin enables refund mode via a 48h timelock.

**Conditions**:

* `market.refundModeActive == true`
* You hold **both `YES` and `NO`**

#### <mark style="color:orange;">How to Claim a Refund</mark>

{% stepper %}
{% step %}
### <mark style="color:orange;">Step 1: Identify Refundable Markets</mark>

* Navigate to your Portfolio.
* Look for markets displaying a Refund available badge. This indicates the event was cancelled or voided.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 2: Initiate the Refund</mark>

* Click the Refund button to open the preview panel.
* Preview details: The system calculates the number of full pairs you hold—`min(yesBalance, noBalance)`—and shows the exact amount of USDC to be returned.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 3: Confirm in Wallet</mark>

* Click Confirm and authorize the transaction in your wallet (e.g., Touch ID for passkey or MetaMask popup).
* The transaction typically processes in \~2 seconds.
{% endstep %}

{% step %}
### <mark style="color:orange;">Step 4: Completion</mark>

* The USDC is returned directly to your wallet balance.
* The corresponding YES + NO token pairs are burned (removed from your holdings) as the collateral is released.
{% endstep %}
{% endstepper %}

### <mark style="color:orange;">Formula</mark>

```
refundAmount = min(yesBalance, noBalance)
payout       = refundAmount USDC (1:1)
```

Example: you hold 100 YES + 80 NO:

* Refund 80 pairs → receive 80 USDC.
* 20 YES remaining → **no NO to pair with**, cannot be refunded.

### :page\_with\_curl: <mark style="color:orange;">Note: refund is pair-based only</mark>

If you hold only one side (e.g. bought 100 YES via the Router, no NO held), you **cannot claim** USDC through the standard refund flow.

**Workaround**:

* Buy NO from someone still selling (NO price typically drops near $0 when refund mode is active since no one expects a win).
* Pair with your YES to refund.
* Your loss is the NO purchase price.

### <mark style="color:orange;">This is a design trade-off</mark>

Refund mode prioritizes **pro-rata fairness** over first-come-first-serve. This prevents a scenario where early claimers drain all USDC, leaving nothing for later users.

Phase 2 (TBA): A **single-sided refund** with a 50% haircut may be introduced — burn 100 YES → receive 50 USDC. Currently under governance discussion.

## Who decides to enable refund

![Admin refund: admin propose enableRefundMode → TimelockController 48h delay → execute → user burn YES+NO pairs → USDC pro-rata](../.gitbook/assets/17-admin-refund.svg)

### Troubleshooting

<details>

<summary><mark style="color:orange;">Redeem button not appearing for a resolved market</mark></summary>

**Reason:** Either you don't hold the winning side, or the indexer hasn't caught up to the resolution event.

**Fix:**

* Check the market's resolved outcome — do you hold YES (if outcome = true) or NO (if outcome = false)?
* If outcome matches your holdings, wait 30 seconds and refresh
* Force a fresh fetch with `?nocache=1` URL param

</details>

<details>

<summary><mark style="color:orange;">"Insufficient balance" on redeem</mark></summary>

**Reason:** Your token balance is lower than the contract expects (possibly due to a recent transfer or pending indexer sync).

**Fix:**

* Verify your token balance on the explorer
* Wait 30 seconds for indexer to sync, then retry
* If balance is genuinely insufficient, you cannot redeem more than you hold

</details>

<details>

<summary><mark style="color:orange;">Batch redeem failed mid-way (EOA wallets)</mark></summary>

**Reason:** EOA wallets process each market as a separate tx. If one fails (e.g., gas spike), the remaining will still process — but you may need to retry the failed ones.

**Fix:**

* Refresh Portfolio — successful redemptions are reflected
* For the failed market(s), click **Redeem** individually
* Consider switching to Passkey for atomic batching

</details>

<details>

<summary><mark style="color:orange;">Refund button shows "Insufficient pair"</mark></summary>

**Reason:** You hold only one side of the market (e.g., 100 YES, 0 NO) and refund requires pairs.

**Fix:** See Single-sided holders — workaround above. Buy the missing side at near-zero price from active sellers, then refund the pair.

</details>

<details>

<summary><mark style="color:orange;">Refund mode active but no sellers for the opposite side</mark></summary>

**Reason:** All holders may also be single-sided (everyone bought the same direction via Router). Liquidity dries up when refund mode is active.

**Fix:**

* Post a limit order on the opposite side at a very low price (`$0.01`)
* Wait for the orderbook to develop — usually sellers appear within 24h
* Discord `#refund-coordination` for matching with counterparties

</details>

<details>

<summary><mark style="color:orange;">Resolved market still shows as "Pending" in Portfolio</mark></summary>

**Reason:** Resolution oracle posted the outcome on-chain but indexer is still syncing.

**Fix:**

* Wait 1–2 minutes for indexer sync
* Verify the resolution on-chain via the explorer
* If stuck >10 minutes, report on Discord `#bug-reports`

</details>

***

