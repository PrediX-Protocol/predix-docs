# PrediX

On-chain prediction market on Unichain. Each event creates two outcome tokens YES / NO; the correct token redeems 1:1 USDC when the market resolves.

> 🟡 **Status**: Beta on **Unichain Sepolia testnet** (chain `1301`). Mainnet (chain `130`) launches after external audit — see [Network info](getting-started/README.md#network-info).

**Example**: Market *"Bitcoin above $100k before 2027?"* creates YES + NO tokens. Users buy the side they believe will win.

| When market resolves | YES holder | NO holder |
|---|---|---|
| Event **happens** | Receives `$1 USDC / token` | `$0` (total loss) |
| Event **doesn't happen** | `$0` (total loss) | Receives `$1 USDC / token` |

![PrediX — On-chain prediction market on Unichain. Users predict YES/NO, trade shares, earn rewards. Transparent, tamper-proof, immutable.](_design/hero-overview.png)

---

## Start here

| You are… | Start from |
|---|---|
| Trader ready to go | [Getting Started](getting-started/README.md) |
| Want to understand the product | [Overview](overview/README.md) |
| Need step-by-step tutorials | [Guides](guides/README.md) |
| Stake PRX + governance + rewards | [Economics](economics/README.md) |
| Builder integrating | [Developers](developers/README.md) |

---

## What makes PrediX different

- **Outcome tokens are ERC-20** — composable with DeFi (LP, collateral, vault, lending).
- **Hybrid CLOB + AMM** — Router automatically splits orders between on-chain order book and Uniswap v4 pool for best execution in a single tx.
- **Real yield** — protocol fees distributed to stakers (real USDC), PRX buyback-burn reduces supply, insurance fund. No emission.
- **Non-custodial** — Router stateless, invariant `balanceOf(router) == 0` enforced on-chain. Diamond + Hook upgrades via 48h timelock, no emergency bypass.
- **Account abstraction** — sign in with passkey (web2 UX) or crypto wallet. Gas sponsor program for eligible users — applies to both account types.

---

## Links

- **App**: [app.predix.app](https://app.predix.app) (testnet beta)
- **Explorer testnet**: [sepolia.uniscan.xyz](https://sepolia.uniscan.xyz)
- **Explorer mainnet**: [uniscan.xyz](https://uniscan.xyz) (post-launch)
- **Contract addresses**: [developers/architecture.md#contract-addresses](developers/architecture.md#contract-addresses) (testnet live + mainnet TBA)
- **Security & audit**: [developers/security.md](developers/security.md)
- **Discord, Twitter, GitHub**: [resources/links.md](resources/links.md)
