# Contract addresses

PrediX hiện ở **testnet beta**. Mainnet sẽ deploy sau external audit hoàn tất — addresses fill khi có.

## Unichain Sepolia — Testnet (chain `1301`) — LIVE

**Deploy**: 2026-04-20.
**Explorer**: [sepolia.uniscan.xyz](https://sepolia.uniscan.xyz).

### Core PrediX

| Contract | Testnet | Mainnet |
|---|---|---|
| **Diamond** | `0x7689E9bf4b2107E2Fd0f1DDA940E2f1143434E39` | TBA |
| **Hook (proxy)** | `0x89830AC92Ff936f39C2D11D1fd821c6f977fAAE0` | TBA |
| **Hook (impl)** | `0x0dcB4624588316d9a8Dd7868EeFBF07532c29E02` | TBA |
| **Exchange** | `0xE425698e1835DA0A6086eEB85137A36275993F41` | TBA |
| **Router** | `0x6698253F38F4A4bbBC4A223309B4E560d83D7ee0` | TBA |
| **ManualOracle** | `0x7887f07AF62CE0a4Cf836136135a61b59c36A9d2` | TBA |
| **ChainlinkOracle** | Disabled (no feed Sepolia) | TBA |
| **TimelockController** | `0x578D2a308BB0aa5d30E6BC08A7975ccA7e88af61` | TBA |
| **Paymaster (ERC-4337)** | `0x1637a7eB463b1b12906feF71eF23B76181340Cb7` | TBA |
| **Faucet (relayed)** | `0x7beD6B3D8397Bc9F77626f84D64BED8894C27350` | N/A (no faucet) |

### Diamond facets

| Facet | Testnet | Mainnet |
|---|---|---|
| DiamondCutFacet | `0xBD5Af6FAdD6B2e3bd5A84B7fD27F34a6Dd0cAc42` | TBA |
| DiamondLoupeFacet | `0x61704bdFBC5c0D2995781E7288FDB36C33AC3F31` | TBA |
| AccessControlFacet | `0xfBA0e94Bd45aaE8256e42d95f9920267b54E63b2` | TBA |
| PausableFacet | `0x4b025374A920fE11285F5e823Be348F3a04f35A9` | TBA |
| MarketFacet | `0xDa9e084439c4C6232ad2ceD8AFdbCb06fAd79BE4` | TBA |
| EventFacet | `0xC28Af5a51424af22eD6d1EF444B1b1Dcd8406822` | TBA |

### External / infrastructure

| Contract | Testnet | Mainnet |
|---|---|---|
| **USDC** | `0x2D56777Af1B52034068Af6864741a161dEE613Ac` (test-USDC) | TBA |
| **PRX token** | Chưa deploy | TBA (mint sau TGE) |
| **Staking Vault** | Chưa deploy | TBA |
| **vePRX** | Chưa deploy | TBA |
| **Uniswap v4 PoolManager** | `0x00b036b58a818b1bc34d502d3fe730db729e62ac` | TBA (per Uniswap deploy) |
| **Permit2** | `0x000000000022D473030F116dDEE9F6B43aC78BA3` | `0x000000000022D473030F116dDEE9F6B43aC78BA3` |
| **EntryPoint v0.7** | `0x0000000071727De22E5E9d8BAf0edAc6f37da032` | `0x0000000071727De22E5E9d8BAf0edAc6f37da032` |

> Permit2 + EntryPoint là canonical addresses — giống nhau across mọi chain.

### Chain config

| | Testnet | Mainnet |
|---|---|---|
| **Network name** | Unichain Sepolia | Unichain |
| **Chain ID** | `1301` | `130` |
| **RPC public** | `https://sepolia.unichain.org` | `https://mainnet.unichain.org` |
| **Block time** | ~1s | ~1s |
| **Finality** | ~12-15 min (L2) | ~12-15 min (L2) |

## Governance addresses

> **Testnet shortcut**: Mọi role testnet collapse vào 1 EOA cho dev test nhanh. Mainnet sẽ phân tách rõ với Gnosis Safe multisig.

| Role | Testnet (1 EOA collapse) | Mainnet setup |
|---|---|---|
| **MULTISIG_ADMIN** | `0x0eC2bFb36BB59C736d7b770eacaFAa43a184De34` | Gnosis Safe 3-of-5, addr TBA |
| **OPERATOR** | same | Gnosis Safe 2-of-3, addr TBA |
| **PAUSER** | same | Gnosis Safe 2-of-3, addr TBA |
| **REPORTER** (oracle) | same | Multisig 3/5, addr TBA |
| **REGISTRAR** | same | Admin multisig, addr TBA |
| **FEE_RECIPIENT** | same | Treasury Safe, addr TBA |
| **CUT_EXECUTOR** | `0x578D2a308BB0aa5d30E6BC08A7975ccA7e88af61` (TimelockController testnet) | TimelockController mainnet, addr TBA, delay 48h |

## Sync với code

App + SDK đồng bộ addresses từ on-chain registry. Không hardcode trong client code.

```javascript
// Ví dụ fetch addresses qua API
const addresses = await fetch('https://api.predix.app/v2/addresses?chain=mainnet').then(r => r.json());
// hoặc chain=testnet cho Unichain Sepolia
```

## Verify source code

Mọi contract source verified trên Uniscan:

```
https://sepolia.uniscan.xyz/address/<ADDRESS>#code   # testnet
https://uniscan.xyz/address/<ADDRESS>#code            # mainnet (sau deploy)
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
