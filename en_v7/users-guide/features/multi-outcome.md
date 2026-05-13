---
description: >-
  An event with multiple choices: "Who will win FIFA WC 2026?" — each team is a
  child market, only one wins.
---

# Multi-outcome events

***

Many real-world questions are not binary. A tournament has dozens of possible winners. An election has multiple candidates. PrediX introduces a native **event** primitive — a container that links N binary markets under a single mutual-exclusion constraint enforced on-chain.

### Structure

> An event is a container holding N child markets (called **members**), all resolving together under one winner.

* Members share a common **endTime**.
* **Mutually exclusive** — when the event resolves, exactly 1 member's YES = true; all others YES = false.
* Supports **groupSplit / groupMerge** — mint/burn YES tokens of all members atomically.

![Event multi-outcome: Event (FIFA WC) → N members (Argentina, Brazil, France...) → each member has YES+NO → resolve winningIndex → winner YES=$1](../../.gitbook/assets/53-event-multi-outcome.svg)

### Example

Event: _"FIFA WC 2026 Winner"_ with 48 teams.

* Each team = 1 binary market: "Will Argentina win?", "Will Brazil win?", ...
* The sum of all 48 YES prices should be approximately $1 (probabilities sum to 100%).

When the event resolves (Argentina wins):

* Argentina YES = $1, NO = $0.
* The remaining 47 teams: YES = $0, NO = $1.

### Trading

#### <mark style="color:orange;">Buy YES for one team</mark>

Click a member in the event UI → the trading panel opens → **Buy YES Argentina** (same as a standard market) → Router swaps USDC → buy YES via CLOB + AMM → you receive YES Argentina.

#### <mark style="color:orange;">Buy NO (short one team)</mark>

Bet that a team **will not** win. Payout = 1 USDC if that team loses or does not reach the final.

#### <mark style="color:orange;">groupSplit — special action</mark>

An action exclusive to events. Mint YES tokens for **all** members in a single transaction:

```
Deposit 1 USDC →
   Mint 1 YES Argentina + 1 YES Brazil + ... + 1 YES Netherlands (all 48)
```

Atomic — 1x mints everything. Bet on **"some team will win"** → exactly 1 token is guaranteed to be worth $1 upon resolution.

<mark style="color:orange;">**Use cases:**</mark>

* Hedging strategies.
* Market-making across multiple teams simultaneously.
* Providing liquidity for the entire event.

#### <mark style="color:orange;">groupMerge</mark>

The reverse: if you hold exactly 1 YES for all 48 members → burn them all → receive 1 USDC.

### Why this design

Events create an **on-chain constraint**: exactly 1 member resolves YES = true. This ensures:

* Total YES payout = collateral deposited — no insolvency, no incorrect wins.
* Arbitrage keeps the sum of member YES prices at exactly $1, reflecting true probabilities.
* Users can trade individual members without needing to know the overall event structure — just look at that team's price.

### Resolution

Event oracle resolves differently from a single market — all members resolve in one atomic call:

```solidity
eventFacet.resolveEvent(eventId, winningIndex: 0)  // Argentina
```

* `winningIndex` is in \[0, N-1] — exactly 1 member is marked YES.
* Atomic: all members resolve in the same block. There is no case where 2 members both resolve YES.

### Event refund

If an event fails (e.g. the tournament is cancelled):

```solidity
eventFacet.enableEventRefundMode(eventId)
```

* All members enter refund mode.
* Users `groupMerge` (if they hold YES for every member) → receive USDC pro-rata.
* Or refund individual members separately (if you only hold some).

### Event UI

The [Events page](https://app.predix.app/events):&#x20;

* Grid displaying events with total volume + deadline.
* Click an event → detail page:
  * **Combined chart**: YES prices of all members on the same timeline.
  * **Members table**: each team in a row showing YES price, volume, and a Buy button.
  * **Right panel**: trading panel for the selected member.

URL sync: `/events/42?outcome=3` → pre-selects member index 3.

### Limits

| Trait                                             | Value                      |
| ------------------------------------------------- | -------------------------- |
| Max members / event                               | 50                         |
| Min members / event                               | 2                          |
| Nested events                                     | Not supported              |
| Weighted outcome (member 30% / member 70% payout) | No — winner-takes-all only |

### Difference from separate binary markets

Do not confuse an event with multiple standalone binary markets — the constraint changes everything:

| Approach                                                 | Sum of YES prices enforced at \~$1? | Automatic arbitrage?          |
| -------------------------------------------------------- | ----------------------------------- | ----------------------------- |
| 2 separate binary markets (Argentina win? + Brazil win?) | No                                  | No                            |
| **Event with Argentina + Brazil as members**             | Yes (enforced on-chain)             | Yes, propagates automatically |

Use an **event** when there is a genuine mutually-exclusive constraint. Use **separate binary markets** when the events are independent.

### Trading strategies for events

#### <mark style="color:orange;">Single-favourite</mark>

Buy YES for the team you believe will win. The simplest approach with one-directional exposure.

#### <mark style="color:orange;">Spread bet</mark>

Buy YES for multiple teams you think have a chance. If any one of them wins, you profit. Lower risk than single-favourite, but lower payout.

#### <mark style="color:orange;">Long shots</mark>

Buy YES for teams few people expect (low price). Rarely correct but the payout is enormous (10–100x).

#### <mark style="color:orange;">Dutch book / arbitrage</mark>

If the sum of all member YES prices exceeds $1 → `groupSplit` USDC, sell each YES individually → earn the spread. Rare, but opportunities do arise.
