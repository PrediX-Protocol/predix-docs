---
description: Claim your winnings or get a refund on PrediX.
---

# Redeem & Refund

Redeem winning tokens for $1 each, or burn YES+NO pairs to reclaim your USDC on unresolved markets. After a market resolves, exchange winning tokens for USDC. If a market cannot be resolved, use the refund flow.

### Redeem vs Refund

<table data-header-hidden><thead><tr><th></th><th width="249"></th><th></th></tr></thead><tbody><tr><td></td><td>🏆 <strong>Redeem</strong></td><td>🛡️ <strong>Refund</strong></td></tr><tr><td><strong>When</strong></td><td><ul><li>Market resolved cleanly</li></ul></td><td><ul><li>Market cannot be resolved (oracle down, dispute hung, multisig unresponsive)</li></ul></td></tr><tr><td><strong>Trigger</strong></td><td><ul><li>Automatic, oracle posts outcome</li></ul></td><td><ul><li>Admin enables via 48h timelock</li></ul></td></tr><tr><td><strong>What you submit</strong></td><td><ul><li>Winning tokens (YES or NO)</li></ul></td><td><ul><li>YES + NO <strong>pairs</strong></li></ul></td></tr><tr><td><strong>Payout</strong></td><td><ul><li><code>$1 per winning share</code></li></ul></td><td><ul><li><code>1 USDC per pair burned</code></li></ul></td></tr><tr><td><strong>Losing tokens</strong></td><td><ul><li>Worthless (<code>$0</code>)</li></ul></td><td><ul><li>Single-sided holders need workaround</li></ul></td></tr></tbody></table>

***

### Redeem — market has resolved

**Conditions:**

* `market.isResolved == true`
* You hold the winning token (YES if outcome=true, NO if outcome=false)

#### How to Redeem Your Winnings

{% stepper %}
{% step %}
<mark style="color:orange;">**Step 1: Locate Finished Markets**</mark>

* Navigate to your [Portfolio](portfolio.md).
* Use the filters to select Resolved markets. This will show you all events that have officially concluded.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 2: Check for Eligibility**</mark>

* Look for the Redeem button next to each market.

{% hint style="info" %}
Note: This button will only appear if you hold the winning token (the side that correctly predicted the outcome).
{% endhint %}
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 3: Review the Preview**</mark>

Click the Redeem button to open a preview window showing:

* Token Count: The total number of winning tokens you are turning in.
* USDC to Receive: The total amount of USDC that will be credited to your account.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 4: Confirm in Wallet**</mark>

* Click Confirm and authorize the transaction in your wallet (e.g., biometric authentication for passkeys or a signature for EOA).
* The transaction typically completes in \~2 seconds on Unichain.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 5: Settlement Complete**</mark>

* The USDC is deposited directly into your wallet balance.
* Winning tokens are burned (removed from circulation), while losing tokens automatically drop to a value of $0.
{% endstep %}
{% endstepper %}

#### Batch redeem

Multiple resolved markets → click **Claim All** → batched via **passkey smart account** (1 click, 1 tx, gas via paymaster). EOA users: each market requires a separate tx (wallets do not support native batching). Both account types receive sponsor coverage if the user qualifies for the program.

![Batch redeeming process](../.gitbook/assets/16-claim-batch.svg)

#### Losing tokens

* **No longer have value**.
* Cannot be redeemed or traded (pool is drained).
* They still appear in your wallet with a balance, but market value = $0. Hide them by removing from your watchlist.

***

### Refund mode — market cannot be resolved

**When**: Oracle is down, dispute is hung, multisig is unresponsive → admin enables refund mode via a 48h timelock.

**Conditions**:

* `market.refundModeActive == true`
* You hold **both `YES` and `NO`**

#### How to Claim a Refund

{% stepper %}
{% step %}
<mark style="color:orange;">**Step 1: Identify Refundable Markets**</mark>

* Navigate to your Portfolio.
* Look for markets displaying a Refund available badge. This indicates the event was cancelled or voided.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 2: Initiate the Refund**</mark>

* Click the Refund button to open the preview panel.
* Preview details: The system calculates the number of full pairs you hold—`min(yesBalance, noBalance)`—and shows the exact amount of USDC to be returned.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 3: Confirm in Wallet**</mark>

* Click Confirm and authorize the transaction in your wallet (e.g., Touch ID for passkey or MetaMask popup).
* The transaction typically processes in \~2 seconds.
{% endstep %}

{% step %}
<mark style="color:orange;">**Step 4: Completion**</mark>

* The USDC is returned directly to your wallet balance.
* The corresponding YES + NO token pairs are burned (removed from your holdings) as the collateral is released.
{% endstep %}
{% endstepper %}

#### Refund Formula

```
refundAmount = min(yesBalance, noBalance)
payout       = refundAmount USDC (1:1)
```

Example: you hold 100 YES + 80 NO:

* Refund 80 pairs → receive 80 USDC.
* 20 YES remaining → **no NO to pair with**, cannot be refunded.

{% hint style="info" %}
#### Note: refund is pair-based only

If you hold only one side (e.g. bought 100 YES via the Router, no NO held), you **cannot claim** USDC through the standard refund flow.

**Alternative solution:**

* Buy NO from someone still selling (NO price typically drops near $0 when refund mode is active since no one expects a win).
* Pair with your YES to refund.
* Your loss is the NO purchase price.
{% endhint %}

***

## Who decides to enable refund

Refund mode prioritizes **pro-rata fairness** over first-come-first-serve. This prevents a scenario where early claimers drain all USDC, leaving nothing for later users.

![Refund decision model](../.gitbook/assets/17-admin-refund.svg)

{% hint style="info" %}
**Phase 2 (TBA)**

* A **single-sided refund** with a 50% haircut may be introduced — burn 100 YES → receive 50 USDC.
{% endhint %}
