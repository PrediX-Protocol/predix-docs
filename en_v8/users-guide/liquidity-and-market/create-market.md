---
description: >-
  Guide for creators (holders of CREATOR_ROLE) to create a market or
  multi-outcome event.
icon: calendar-check
---

# Create Market

Apply for **`CREATOR_ROLE`**: submit a form on [**`Discord`**](../../resources/links.md) **`#creator-application`** + governance vote.

| Phase                 | Who                                                        | Requirements                                                                                                                  |
| --------------------- | ---------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| **Phase 1 - Current** | <ul><li>Addresses with <code>CREATOR_ROLE</code></li></ul> | <ul><li>Whitelisted via governance proposal</li></ul>                                                                         |
| **Phase 3 - TBA**     | <ul><li>Permissionless</li></ul>                           | <ul><li>Stake a bond of <strong>10,000 PRX</strong> (refunded if the market resolves cleanly, slashed if malformed)</li></ul> |

***

### Decisions Before Creating

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

***

### Parameters To Decide

<details>

<summary><mark style="color:$primary;"><strong>Question</strong></mark></summary>

The main question for the market - it must be:

* **Specific**: "Will BTC exceed $100,000 (USD) on CoinGecko close 2027-01-01 UTC?" - not "Will BTC go high?"
* **Falsifiable**: Can be answered definitively YES/NO using an objective source.
* **Time-bound**: Has a clear endTime.
* **Unambiguous**: Avoid "maybe", "approximately", "around".

</details>

<details>

<summary><mark style="color:$primary;"><strong>endTime</strong></mark></summary>

The timestamp when trading closes and the oracle resolution window opens.

* Format: Unix seconds (UTC).
* Min: 1 hour from creation (prevents front-running).
* Max: 5 years (UI cap).

</details>

<details>

<summary><mark style="color:$primary;"><strong>Oracle</strong></mark></summary>

| Oracle              | When to use                      | Setup cost                          |
| ------------------- | -------------------------------- | ----------------------------------- |
| **ChainlinkOracle** | Price threshold                  | Free, register feed + threshold     |
| **ManualOracle**    | Subjective event                 | Free, 2/3 multisig resolves         |
| **UMAOracle** (TBA) | Decentralized resolution         | Bond $500-$50,000 USDC              |
| **Custom adapter**  | On-chain event (governance, TVL) | Deploy adapter, approve via Diamond |

</details>

<details>

<summary><mark style="color:$primary;"><strong>Per-market cap (optional)</strong></mark></summary>

Limits the total collateral locked in the market. Default = global cap (protocol config).

* Set 0 = unlimited.
* Set X = max X USDC collateral. Provides protection when a market is high-risk.

</details>

<details>

<summary><mark style="color:$primary;"><strong>Category + Metadata</strong></mark></summary>

Off-chain metadata for a polished UI display:

* **Title** (LocalizedString - vi/en/ja/ko)
* **Description**
* **Category**: crypto, sports, politics, weather, AI, ...
* **Featured**: boolean
* **Image / icon**

Set via the admin BE endpoint after on-chain creation.

</details>

***

### Create a Binary Market with Chainlink

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

{% stepper %}
{% step %}
<mark style="color:$warning;">**Step 1: Create the On-chain Market**</mark>

```
diamond.createMarket(
  question: "Will BTC exceed $100k before 2027-01-01?",
  endTime: 1798752000,  // Unix sec
  oracle: ChainlinkOracle.address
)
```

Returns `marketId` + addresses of the YES and NO ERC-20 tokens.
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 2: Register the pool with Hook**</mark>

```
hook.registerMarketPool(marketId, {
  currency0: USDC,
  currency1: yesToken,
  fee: DYNAMIC_FEE_FLAG,            // hook decides
  tickSpacing: 60,
  hooks: PrediXHook.address
}, yesIsCurrency0: false)
```
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 3: Seed Initial Liquidity**</mark>

* See details: [Liquidity Provider](provide-liquidity.md).
{% endstep %}

{% step %}
<mark style="color:$warning;">**Step 4: Set Metadata**</mark>

**Set metadata** (UI title, category, image) via the BE admin endpoint:

```
POST /api/v1/admin/markets/:id/display
{
  title: { vi: "...", en: "...", ja: "...", ko: "..." },
  category: "crypto",
  image: "ipfs://...",
  isFeatured: false
}
```
{% endstep %}
{% endstepper %}

***

### Create a multi-outcome event

An event is a container holding N child markets. Upon resolution, exactly 1 member resolves **YES = true**; the rest resolve **YES = false**.

```
diamond.createEvent(
  name: "FIFA WC 2026 Winner",
  candidateQuestions: [
    "Will Argentina win?",
    "Will Brazil win?",
    "Will France win?",
    ...  // 48 teams
  ],
  endTime: 1789300000,  // after the final match
  oracle: ManualOracle.address
)
```

Returns `eventId` + an array of `marketIds`. Each member is a standard binary YES/NO market that belongs to the event.

After event resolution:

```
eventFacet.resolveEvent(eventId, winningIndex: 0)  // Argentina wins
```

Atomic - all members resolve in the same block; exactly 1 outcome = true.

***

### Best Practices

* **Question wording**: Test with 5 people unfamiliar with the context. Can they answer YES/NO?
* **endTime buffer**: Set at least 1-2 hours after the actual event so data can finalize before resolution.
* **Oracle source**: For ManualOracle, document the primary source in the description.
* **Liquidity seeding**: Minimum $500-$1000 USDC + corresponding YES/NO. A pool that is too shallow drives users away.
* **Featured marketing**: Coordinate with the community team before launching a large market.

***

### Resolve dispute - you are the creator

If a market resolves incorrectly:

* **Phase 1 ManualOracle**: You are not a multisig member - flag on Discord and the multisig will review. If incorrect -> refund mode is enabled.
* **Phase 2 UMAOracle**: Anyone can propose a dispute with a bond.
* **Phase 1 ChainlinkOracle**: Resolution is automatic - disputes are only possible if the Chainlink feed was manipulated; escalate via Chainlink directly.

***

### Revenue for creators (Phase 3 - TBA)

Phase 3 will introduce **creator revenue share** - a portion of the protocol fees from that market goes to the creator. Details will be announced per the roadmap.
