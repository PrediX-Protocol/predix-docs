---
icon: calendar-days
---

# Allocation & Vesting

PRX has a fixed total supply of 1,000,000,000 tokens (1B). There is no additional minting mechanism after TGE - the supply is hard-capped at the protocol level and enforced on-chain.

The allocation framework is built around a low-float launch design, with only **110.1M PRX (11.01% of total supply)** circulating at TGE. This structure is engineered to protect early token value, align long-term contributors with sustained protocol performance, and prevent supply shocks during the bootstrap phase.

***

### Bucket Strategy

<details>

<summary><mark style="color:$warning;"><strong>Community &#x26; Ecosystem - 35% (350M)</strong></mark></summary>

The largest allocation in the supply distribution, reflecting PrediX's commitment to a community-first economic model. Distributed across six emission seasons (S0–S6) spanning three years, with rewards declining approximately 30% per season to front-load incentives during the most critical bootstrap phase.

The Season 1 (Genesis) pool is further structured as a 3-wave TGE release - 40% at TGE, 30% at TGE + 30 days, and 30% at TGE + 60 days - designed to reduce concentrated sell pressure and reward holders who stake early waves to boost subsequent waves.

</details>

<details>

<summary><mark style="color:$warning;"><strong>Investors (All Rounds) - 20% (200M)</strong></mark></summary>

Strategic capital sourced across two private rounds:

* Pre-seed: 125M PRX at $0.016
* Seed: 75M PRX at $0.04 – $0.05

TGE unlock is intentionally constrained at 8.8% (17.6M PRX), followed by a 6-month cliff and 24-month linear vesting, aligning investor incentives with multi-year protocol growth.

</details>

<details>

<summary><mark style="color:$warning;"><strong>Team &#x26; Advisors - 8% (80M)</strong></mark></summary>

Subject to the strictest vesting schedule in the allocation: zero tokens at TGE, a 12-month cliff, followed by 36 months of linear vesting (48 months total).

The Team allocation is paired with a performance acceleration framework, allowing vesting velocity to adjust based on measurable protocol milestones. The vesting contract is deployed on-chain and publicly verifiable.

</details>

<details>

<summary><mark style="color:$warning;"><strong>Treasury / DAO - 17% (170M)</strong></mark></summary>

The protocol-owned treasury, governed by vePRX voting. Funds are allocated across four primary use cases:

* Development funding and contributor grants
* External audits and security operations
* LP gauge subsidies directing liquidity toward voted markets
* Insurance fund top-ups

Disbursements above $10,000 require an on-chain governance vote. The treasury is managed via a multisig with a 48-hour timelock on execution.

</details>

<details>

<summary><mark style="color:$warning;"><strong>Marketing &#x26; Partners - 15% (150M)</strong></mark></summary>

Reserved for KOL and ambassador programs, centralized and decentralized exchange listings, brand partnerships, and ecosystem activations. 3-month cliff with 24-month linear vesting ensures growth capital is deployed steadily across the multi-year scaling phase.

</details>

<details>

<summary><mark style="color:$warning;"><strong>Liquidity (DEX/CEX) - 5% (50M)</strong></mark></summary>

Fully unlocked at TGE to bootstrap immediate market depth. Deployed across Uniswap v4 pools and used to seed centralized exchange market making, ensuring tradable liquidity from day one.

</details>

***

### TGE Circulating Breakdown

Here is the breakdown of the **110.1M PRX (11.01% of total supply)** entering circulation at TGE:

| **Allocation**                                                   | **Unlock Token**                                                | **TGE Float - %**                                        |
| ---------------------------------------------------------------- | --------------------------------------------------------------- | -------------------------------------------------------- |
| Liquidity (DEX/CEX)                                              | 50,000,000 - 100%                                               | 45.4%                                                    |
| Community & Ecosystem                                            | 35,000,000 - 10%                                                | 31.8%                                                    |
| Investors                                                        | 17,600,000 - 8.8%                                               | 16.0%                                                    |
| Marketing & Partners                                             | 7,500,000 - 5%                                                  | 6.8%                                                     |
| Team & Advisors                                                  | 0 - 0%                                                          | 0%                                                       |
| Treasury / DAO                                                   | 0 - 0%                                                          | 0%                                                       |
| <mark style="background-color:$warning;">**Total at TGE**</mark> | <mark style="background-color:$warning;">**110,100,000**</mark> | <mark style="background-color:$warning;">**100%**</mark> |

This design isolates long-term contributors and protocol reserves from the initial market and ensures that early float is dominated by liquidity provisioning and community distribution.

{% hint style="success" %}
**Zero tokens from Team and Treasury allocations enter circulation at TGE.**&#x20;
{% endhint %}

***

### Conditions-Based TGE

**PrediX does not commit to a fixed TGE date.** Instead, the protocol applies a conditions-based TGE model - token generation occurs only after the network reaches predefined performance thresholds that demonstrate genuine product–market fit.

This approach protects token value from a premature launch and ensures that PRX enters the market backed by measurable, sustained network activity.

#### <mark style="color:$warning;">Activation Gates</mark>

| **Metric**                  | **Threshold**               | **Sustained Period**                    |
| --------------------------- | --------------------------- | --------------------------------------- |
| Monthly Trading Volume      | ≥ $500,000                  | 3 consecutive months                    |
| Weekly Active Traders (WAT) | ≥ 1,000                     | 3 consecutive months                    |
| Active Markets              | ≥ 10 concurrent             | 3 consecutive months                    |
| Smart Contract Audit        | 0 critical, 0 high findings | Independent third-party audit completed |

This framework converts TGE from a marketing event into a technical and economic milestone - one that the network earns rather than schedules.

{% hint style="info" %}
**All four conditions must be satisfied before TGE is initiated. Progress against each metric is reported publicly, and the audit outcome is published to the official documentation prior to activation.**
{% endhint %}
