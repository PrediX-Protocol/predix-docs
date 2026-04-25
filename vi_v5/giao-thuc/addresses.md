# Contract addresses

Danh sách địa chỉ deploy. Single source of truth: `SC/testenv.local` (gitignored).

## Unichain Sepolia (testnet, chain 1301)

**Deploy ngày**: 2026-04-20. **Explorer**: [sepolia.uniscan.xyz](https://sepolia.uniscan.xyz).

### Core PrediX

| Contract | Address |
|---|---|
| **Diamond** | `0x7689E9bf4b2107E2Fd0f1DDA940E2f1143434E39` |
| **Hook (proxy)** | `0x89830AC92Ff936f39C2D11D1fd821c6f977fAAE0` |
| **Hook (impl)** | `0x0dcB4624588316d9a8Dd7868EeFBF07532c29E02` |
| **Exchange** | `0xE425698e1835DA0A6086eEB85137A36275993F41` |
| **Router** | `0x6698253F38F4A4bbBC4A223309B4E560d83D7ee0` |
| **ManualOracle** | `0x7887f07AF62CE0a4Cf836136135a61b59c36A9d2` |
| **TimelockController** | `0x578D2a308BB0aa5d30E6BC08A7975ccA7e88af61` |
| **Paymaster (ERC-4337)** | `0x1637a7eB463b1b12906feF71eF23B76181340Cb7` |
| **Faucet (relayed)** | `0x7beD6B3D8397Bc9F77626f84D64BED8894C27350` |

### Diamond facets

| Facet | Address |
|---|---|
| DiamondCutFacet | `0xBD5Af6FAdD6B2e3bd5A84B7fD27F34a6Dd0cAc42` |
| DiamondLoupeFacet | `0x61704bdFBC5c0D2995781E7288FDB36C33AC3F31` |
| AccessControlFacet | `0xfBA0e94Bd45aaE8256e42d95f9920267b54E63b2` |
| PausableFacet | `0x4b025374A920fE11285F5e823Be348F3a04f35A9` |
| MarketFacet | `0xDa9e084439c4C6232ad2ceD8AFdbCb06fAd79BE4` |
| EventFacet | `0xC28Af5a51424af22eD6d1EF444B1b1Dcd8406822` |
| DiamondInit | `0xE87d8Ba8348377759dfBf8bD9098F0feBAA57556` |
| MarketInit | `0x046AabCE8a69D1B3774276bc6641AB2E2FB5A89D` |

### External / infrastructure

| Contract | Address |
|---|---|
| **USDC (test)** | `0x2D56777Af1B52034068Af6864741a161dEE613Ac` |
| **Uniswap v4 PoolManager** | `0x00b036b58a818b1bc34d502d3fe730db729e62ac` |
| **Permit2** | `0x000000000022D473030F116dDEE9F6B43aC78BA3` |
| **V4 Quoter** | `0x56dcd40a3f2d466f48e7f48bdbe5cc9b92ae4472` |
| **V4 PositionManager** | `0xf969aee60879c54baaed9f3ed26147db216fd664` |
| **V4 UniversalRouter** | `0xf70536b3bcc1bd1a972dc186a2cf84cc6da6be5d` |
| **V4 StateView** | `0xc199f1072a74d4e905aba1a84d9a45e2546b6222` |
| **EntryPoint v0.7** | `0x0000000071727De22E5E9d8BAf0edAc6f37da032` |

### Chain config

- **Chain ID**: `1301`
- **RPC (public)**: `https://sepolia.unichain.org`
- **Deploy block**: `49799033` (Indexer START_BLOCK = 49799032)
- **Chainlink enabled**: `false` (Sepolia không có feed)

### RPC alternatives (test user)

- QuickNode: `https://cold-warmhearted-snow.unichain-sepolia.quiknode.pro/...`
- Public: `https://sepolia.unichain.org`

## Unichain Mainnet (chain 130)

**Trạng thái**: Chưa deploy. Dự kiến Q2 2026 sau audit bên ngoài xong.

Sẽ cập nhật tại đây khi deploy.

## Governance addresses (testnet)

**Warning**: Testnet collapsed mọi role xuống 1 EOA để test nhanh. Mainnet phân tách rõ.

| Role | Testnet address | Mainnet plan |
|---|---|---|
| MULTISIG | `0x0eC2bFb36BB59C736d7b770eacaFAa43a184De34` | Gnosis Safe 3-of-5 |
| OPERATOR | `0x0eC2bFb36BB59C736d7b770eacaFAa43a184De34` | Gnosis Safe 2-of-3 |
| PAUSER | `0x0eC2bFb36BB59C736d7b770eacaFAa43a184De34` | Gnosis Safe 2-of-3 |
| REPORTER | `0x0eC2bFb36BB59C736d7b770eacaFAa43a184De34` | Oracle committee |
| REGISTRAR | `0x0eC2bFb36BB59C736d7b770eacaFAa43a184De34` | Admin multisig |
| FEE_RECIPIENT | `0x0eC2bFb36BB59C736d7b770eacaFAa43a184De34` | Treasury Safe |
| CUT_EXECUTOR | `0x578D2a308BB0aa5d30E6BC08A7975ccA7e88af61` (TimelockController) | Same, 48h delay |

## Deploy history (testnet)

Chỉ để tham khảo forensics, **không dùng** các address dưới đây:

- **2026-04-20** (current): theo bảng trên. Fresh deploy, supersedes Apr-13 và Apr-17.
- **2026-04-17** (Phase 7): Diamond `0xC069159...`, Exchange `0xdEE07224...`, Router `0xf93B62Cc...`, Hook `0x5eF25d02...`.
- **2026-04-15** (initial): Exchange `0xc68dfc34...`.

**Không dùng** các address pre-Apr-15 — không có broadcast backing.

## Sync

FE đồng bộ addresses từ SC qua script `npm run sync:addresses`:
- **Source**: `SC/testenv.local`.
- **Target**: `FE/src/contracts/testnet-addresses.json`.
- **CI guard**: `npm run check:addresses-sync` fail nếu drift.

## Verify source code

Mọi contract có source verified trên Uniscan Sepolia:

```
https://sepolia.uniscan.xyz/address/<ADDRESS>#code
```

Ví dụ Diamond: [sepolia.uniscan.xyz/address/0x7689E9bf4b2107E2Fd0f1DDA940E2f1143434E39](https://sepolia.uniscan.xyz/address/0x7689E9bf4b2107E2Fd0f1DDA940E2f1143434E39).
