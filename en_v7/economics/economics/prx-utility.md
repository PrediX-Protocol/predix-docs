---
description: The native protocol token of PrediX.
icon: list-tree
---

# PRX Utility

PRX is the native protocol token of PrediX, designed as the coordination layer across the entire prediction market ecosystem. Token utility is structured around three interconnected dimensions: economic participation, protocol governance, and product access.&#x20;

***

### Economic Participation

Token holders capture a share of protocol revenue through staking and benefit from a long-term deflationary supply structure.

#### <mark style="color:$warning;">**1. Stake → Real Yield (Available at TGE)**</mark>

Users deposit PRX into the Staking Vault and receive stkPRX, a non-transferable receipt token representing their claim on protocol revenue.

* Each epoch (weekly), the protocol distributes its USDC fee share to the vault pro-rata.
* Yield is denominated in real USDC sourced from actual trading activity - no token emissions, no inflationary dilution.
* Holders may claim USDC at any time or auto-compound back into PRX.
* Unstaking is subject to a 7-day cooldown to prevent front-running around major market events.

#### <mark style="color:$warning;">**2. Lock → vePRX (Available at TGE)**</mark>

Holders may lock PRX between 1 and 48 months to receive vePRX (vote-escrowed PRX), granting boosted yield and governance weight.

| **Lock Duration** | **Yield Boost** | **vePRX Weight** |
| ----------------- | --------------- | ---------------- |
| No lock (stkPRX)  | 1.0×            | 0                |
| 3 months          | 1.1×            | 0.25×            |
| 6 months          | 1.25×           | 0.5×             |
| 12 months         | 1.5×            | 1.0×             |
| 24 months         | 2.0×            | 2.0×             |
| 48 months (max)   | 2.5×            | 4.0×             |

Longer lock durations align token holders with the long-term performance of the protocol and reduce circulating supply pressure.

#### <mark style="color:$warning;">**3. Buyback & Burn (Active from Season 2 onward)**</mark>

A portion of protocol fees collected in USDC is used to purchase PRX from the open market and permanently burn the tokens, sending them to a verifiable burn address.

* [x] Burns are executed monthly, with randomized timing and split transactions to prevent front-running.
* [x] The BuybackExecuted(usdcSpent, prxBurned) event is emitted on-chain for full transparency.
* [x] Once vesting completes after Year 4 and emissions approach zero, PRX is designed to become net deflationary as long as trading volume is sustained.

***

### Protocol Governance

PRX enables decentralized coordination of liquidity, parameters, and treasury through the vePRX framework.

#### <mark style="color:$warning;">**1. Gauge Voting (Available at TGE)**</mark>

Each market on PrediX has a corresponding gauge. vePRX holders vote weekly to direct LP subsidies from the treasury toward specific gauges.

This creates a liquidity coordination mechanism in which:

* [x] Liquidity providers vote to deepen their own pools.
* [x] Market creators compete for liquidity allocation.
* [x] Traders direct subsidies toward markets they want to see scale.

The result is an adaptive incentive system where liquidity flows toward markets with the highest demand, rather than being statically allocated by the team.

#### <mark style="color:$warning;">**2. Bribe Earning (Phase 2)**</mark>

vePRX holders may receive bribes - direct payments in PRX or USDC - from external projects, market creators, or DAOs seeking to attract voting power toward their gauges. This transforms voting rights into a productive economic asset and establishes a liquid market for governance influence.

PrediX will deploy a self-hosted bribe layer in Phase 2.

#### <mark style="color:$warning;">**3. Protocol Parameter Governance (TGE + 12 months)**</mark>

vePRX holders vote on adjustments to core protocol parameters, including:

* Dynamic fee tiers and dispute window durations
* Buyback frequency and burn rate
* Stake cooldown periods
* Phase transitions (Bootstrap → Scale → Mature → Dominance)
* Revenue split between stakers, treasury, and buyback

All proposals follow a transparent flow: 7-day discussion → 5-day voting → 48-hour timelock → multisig execution.

#### <mark style="color:$warning;">**4. Treasury Spend Governance (TGE + 18 months)**</mark>

vePRX holders direct treasury allocation toward audits, contributor grants, marketing initiatives, and insurance fund top-ups. Spending above $10,000 requires a governance vote.

***

### Market Integrity & Security

PRX serves as economic collateral for the operations that secure the protocol from spam and incorrect resolution.

<details>

<summary><mark style="color:$warning;"><strong>Market Creation Bond (Phase 2)</strong></mark></summary>

To create a permissionless market, a user must lock a predefined amount of PRX as a bond.

* Valid markets → bond is refunded upon resolution, and the creator receives a share of trading fees from that market.
* Spam or malicious markets → bond is slashed and redirected to the treasury.

This economic alignment enables open market creation while maintaining quality, eliminating the need for manual gatekeeping.

</details>

<details>

<summary><mark style="color:$warning;"><strong>Oracle Dispute Bond (Phase 2)</strong></mark></summary>

Users may challenge an oracle resolution they believe to be incorrect by posting a PRX bond.

* Correct dispute → bond refunded, plus a reward funded by the slashed bond of the incorrect party.
* Incorrect dispute → bond slashed.

This mechanism creates an economic incentive for the community to actively verify market outcomes, strengthening resolution integrity.

</details>

***

### Product Access & Boosts

Beyond the core protocol, PRX unlocks enhanced functionality across PrediX's consumer-facing products. This layer transforms PRX from a governance and yield instrument into a product utility token experienced by end users in their day-to-day activity.

<details>

<summary><mark style="color:$warning;"><strong>Liquidity Manager</strong></mark></summary>

* Stake PRX to receive a yield boost on LP fees earned in the corresponding pool.
* Hold PRX to unlock premium analytics: real-time P\&L tracking, full-pool liquidity distribution heatmaps, and advanced LP monitoring metrics.

</details>

<details>

<summary><mark style="color:$warning;"><strong>BNPL (Buy Now, Pay Later)</strong></mark></summary>

* Stake PRX as supplemental collateral to reduce the installment interest rate on prediction positions opened via BNPL.
* PRX holders are eligible for higher BNPL credit limits, scaled to stake size and holding duration.

</details>

<details>

<summary><mark style="color:$warning;"><strong>Crowdfunded Market Creation</strong></mark></summary>

* Stake-to-propose: Users must stake PRX to submit a new market proposal to the Market Kickstarter.
* Boost priority: Backers can stake additional PRX to elevate a proposal's visibility during the 7-day commitment window.

</details>

<details>

<summary><mark style="color:$warning;"><strong>Group Buying / Team Purchase</strong></mark></summary>

* Hold PRX to create Team Buy Links and lead group purchase campaigns.
* Group staking unlocks additional discount tiers beyond the standard 2–5% bulk discount.
* KOLs and verified group leaders gain access to advanced campaign features through PRX holdings.

</details>

<details>

<summary><mark style="color:$warning;"><strong>Lossless Lottery</strong></mark></summary>

* Stake PRX to increase the number of lottery tickets allocated per epoch, boosting the odds of winning the random jackpot.
* PRX holders are eligible for a separate bonus jackpot pool, funded by a portion of the management fee collected from the Lossless Pool.

</details>
