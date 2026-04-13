---
description: Smart contract architecture and deployment addresses
---

# Contract Overview

## Architecture

PrediX Protocol consists of 5 core contracts:

| Contract | Address (Unichain Sepolia) | Role |
| -------- | -------------------------- | ---- |
| **Diamond** | `0xF38a265E6e4F57D000a1CC08004da5B4A380B08A` | Market management (EIP-2535 proxy) |
| **Hook** | `0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0` | Uniswap v4 hook: dynamic fees + anti-sandwich |
| **Exchange** | `0xa202abCb2A358c0862B2dA76b553398339F2C638` | On-chain CLOB order book |
| **Router** | `0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7` | Smart routing: CLOB + AMM aggregation |
| **Oracle** | `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3` | ManualOracleAdapter |

### External Dependencies

| Contract | Address |
| -------- | ------- |
| USDC | `0x12fd156C8b5F2901BA2781d97db84AaC56b2b911` |
| Uniswap v4 PoolManager | `0x00B036B58a818B1BC34d502D3fE730Db729e62AC` |

## Why Diamond Proxy (EIP-2535)

- **Bypass 24KB limit**: Complex protocol logic exceeds EVM contract size limit
- **Granular upgrades**: Replace individual functions without redeploying everything
- **Shared storage**: All facets share the same storage layout
- **Production-proven**: Used by Aavegotchi, Beanstalk, and others

## Chain Information

| Parameter | Value |
| --------- | ----- |
| Network | Unichain Sepolia (Testnet) |
| Chain ID | `1301` |
| RPC | `https://sepolia.unichain.org` |
| Explorer | `https://sepolia.uniscan.xyz` |

---

**Next**: [Diamond Contract](diamond.md) · [Exchange](exchange.md) · [Router](router.md) · [Hook](hook.md) · [Oracle](oracle.md)
