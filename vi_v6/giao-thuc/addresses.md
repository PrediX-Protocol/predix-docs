# Contract addresses

## Unichain Mainnet (chain `130`)

**Deploy**: TBA (sau external audit hoàn tất).
**Explorer**: [uniscan.xyz](https://uniscan.xyz).

### Core PrediX

| Contract | Address |
|---|---|
| **Diamond** | `TBA` |
| **Hook (proxy)** | `TBA` |
| **Hook (impl)** | `TBA` |
| **Exchange** | `TBA` |
| **Router** | `TBA` |
| **ManualOracle** | `TBA` |
| **ChainlinkOracle** | `TBA` |
| **TimelockController** | `TBA` |
| **Paymaster (ERC-4337)** | `TBA` |

### Diamond facets

| Facet | Address |
|---|---|
| DiamondCutFacet | `TBA` |
| DiamondLoupeFacet | `TBA` |
| AccessControlFacet | `TBA` |
| PausableFacet | `TBA` |
| MarketFacet | `TBA` |
| EventFacet | `TBA` |

### External / infrastructure

| Contract | Address |
|---|---|
| **USDC** | `TBA` |
| **PRX token** | `TBA` (mint sau TGE) |
| **Staking Vault** | `TBA` |
| **vePRX** | `TBA` |
| **Uniswap v4 PoolManager** | TBA (per Uniswap deploy) |
| **Permit2** | `0x000000000022D473030F116dDEE9F6B43aC78BA3` (canonical, all chains) |
| **EntryPoint v0.7** | `0x0000000071727De22E5E9d8BAf0edAc6f37da032` (canonical) |

### Chain config

| | |
|---|---|
| **Chain ID** | `130` |
| **RPC public** | `https://mainnet.unichain.org` |
| **Block time** | ~1s |
| **Finality** | ~12-15 min (L2) |

## Governance addresses (mainnet)

| Role | Setup | Address |
|---|---|---|
| **MULTISIG_ADMIN** | Gnosis Safe 3-of-5 | `TBA` |
| **OPERATOR** | Gnosis Safe 2-of-3 | `TBA` |
| **PAUSER** | Gnosis Safe 2-of-3 | `TBA` |
| **REPORTER** (oracle) | Multisig 3/5 | `TBA` |
| **REGISTRAR** | Admin multisig | `TBA` |
| **FEE_RECIPIENT** | Treasury Safe | `TBA` |
| **CUT_EXECUTOR** | TimelockController (48h delay) | `TBA` |

## Sync với code

App + SDK đồng bộ addresses từ on-chain registry. Không hardcode trong client code.

```javascript
// Ví dụ fetch addresses qua API
const addresses = await fetch('https://api.predix.app/v2/addresses').then(r => r.json());
```

## Verify source code

Mọi contract source verified trên Uniscan:

```
https://uniscan.xyz/address/<ADDRESS>#code
```

Hoặc qua Sourcify mirror.

## ABI files

ABI public ở:
- npm package: `@predix/abi` (TBA)
- GitHub: `github.com/predix-protocol/abi`
- Direct download: `https://api.predix.app/v2/abi/<contract>`

Chi tiết: [Tích hợp Router](../developers/router-integration.md).

## Bytecode hash

Audit firm verify bytecode hash khớp với source verified:

```
keccak256(deployedBytecode) = TBA per contract
```

Published trong audit report.

## Multi-chain (TBA)

Phase 3 — deploy multi-chain qua bridge:

| Chain | Status |
|---|---|
| Unichain | Primary (deployed first) |
| Base | TBA |
| Arbitrum | TBA |
| Optimism | TBA |
| Polygon | TBA |

Cross-chain message qua Wormhole / LayerZero. Detail: roadmap.

## Testnet

Cho dev integrate trước mainnet, xem [Testnet info](../developers/testnet.md).
