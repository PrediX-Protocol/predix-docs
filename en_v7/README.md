---
description: The Financializing Information Markets
---

# Welcome to PrediX

PrediX is the On-chain Prediction Market on [Unichain](https://www.unichain.org/). Each event creates two outcome tokens `YES / NO` and the correct token redeems 1:1 USDC when the market resolves.

> 🟡 **Status —** see on [**Network info**](getting-started/#network-info)**.**
>
> * Beta on **Unichain Sepolia testnet** (chain `1301`).&#x20;
> * Mainnet launches after **external audit** (chain `130`).

**Example**: Market _"Bitcoin above $100k before 2027?"_ creates YES + NO tokens. Users buy the side they believe will win.

| When Market Resolves     | YES holder                 | NO holder                  |
| ------------------------ | -------------------------- | -------------------------- |
| Event **happens**        | Receives `$1 USDC / token` | `$0` (total loss)          |
| Event **doesn't happen** | `$0` (total loss)          | Receives `$1 USDC / token` |

![PrediX — The Financializing Information Markets](.gitbook/assets/hero-overview.png)

***

## Start here

| You are…                         | Start from                           |
| -------------------------------- | ------------------------------------ |
| Trader ready to go               | [Getting Started](getting-started/)  |
| Want to understand the product   | [Overview](fundamentals/overview.md) |
| Need step-by-step tutorials      | [Guides](guides/)                    |
| Stake PRX + governance + rewards | [Economics](economics/)              |
| Builder integrating              | [Developers](developers/)            |

***

## What makes PrediX different

* **Outcome tokens are ERC-20** — composable with DeFi (LP, collateral, vault, lending).
* **Hybrid CLOB + AMM** — Router automatically splits orders between on-chain order book and Uniswap v4 pool for best execution in a single tx.
* **Real yield** — protocol fees distributed to stakers (real USDC), PRX buyback-burn reduces supply, insurance fund. No emission.
* **Non-custodial** — Router stateless, invariant `balanceOf(router) == 0` enforced on-chain. Diamond + Hook upgrades via 48h timelock, no emergency bypass.
* **Account abstraction** — sign in with passkey (web2 UX) or crypto wallet. Gas sponsor program for eligible users — applies to both account types.

***

## Links

* **App**: [app.predix.app](https://app.predix.app) (testnet beta)
* **Explorer testnet**: [sepolia.uniscan.xyz](https://sepolia.uniscan.xyz)
* **Explorer mainnet**: [uniscan.xyz](https://uniscan.xyz) (post-launch)
* **Contract addresses**: [developers/architecture.md#contract-addresses](developers/architecture.md#contract-addresses) (testnet live + mainnet TBA)
* **Security & audit**: [developers/security.md](developers/security.md)
* **Discord, Twitter, GitHub**: [resources/links.md](resources/links.md)
