# Architecture & contracts

Solidity `0.8.30`, Foundry, EVM cancun (EIP-1153 transient storage). 7 packages, monorepo. Deployed addresses (testnet + mainnet TBA) at the bottom of this page.

## Dependency graph

![SC dependency: shared -> oracle/diamond -> hook/exchange -> router. Cross-package import only via @predix/shared/interfaces](../_design/08-sc-dependency.svg)

Rule: cross-package imports are **only** allowed via `@predix/shared/interfaces/`. No importing implementations from other packages.

## Diamond (EIP-2535)

Single proxy `PrediX Diamond` with 6 facets. Each facet is independently upgradeable.

| Facet | Responsibility |
|---|---|
| **MarketFacet** | createMarket - split - merge - resolve - redeem - emergencyResolve - refundMode - sweep |
| **EventFacet** | createEvent - resolveEvent - groupSplit - groupMerge - refundMode event |
| **AccessControlFacet** | grantRole - revokeRole - 6 roles: DEFAULT_ADMIN - OPERATOR - PAUSER - CUT_EXECUTOR - CREATOR - REGISTRAR |
| **PausableFacet** | pause(module) - unpause(module) — per-module pause: MARKET - DIAMOND |
| **DiamondCutFacet** | diamondCut — add/replace/remove facets, gated by `CUT_EXECUTOR_ROLE` via TimelockController 48h |
| **DiamondLoupeFacet** | facets() - facetAddresses() - facetFunctionSelectors() — introspection |

**Storage**: Diamond storage pattern. Each facet has a `Layout` struct at slot `keccak256("predix.storage.<module>")`. **Append-only** — no reordering or removing fields.

## Hook (Uniswap v4)

**Contract**: `PrediXHookV2` (implementation) + `PrediXHookProxyV2` (ERC1967 proxy).

**Callbacks** set via permissions flag in the hook address (salt-mined):

| Callback | Responsibility |
|---|---|
| `beforeInitialize` | Set permission flag + init pool state |
| `beforeAddLiquidity` | Block adding LP if market is resolved / refunded |
| `beforeRemoveLiquidity` | Track pool registration (hookPoolBinding) |
| `beforeSwap` | Verify anti-sandwich identity (EIP-1153 transient storage) |
| `afterSwap` | No-op |
| `beforeDonate` | Block donate after endTime (prevents brute-force payout attack) |

**Key functions**:
- `registerMarketPool(marketId, poolKey, yesIsCurrency0)` — binds market to v4 pool, verifies canonical PoolKey (lpFee + tickSpacing match)
- `commitSwapIdentityFor(...)` — Router commits identity before swap, Hook verifies in `beforeSwap`
- `proposeTrustedRouter` / `executeTrustedRouter` — 2-step Router rotation (48h timelock)

### Hook proxy upgrade — 48h monotonic timelock

![Hook proxy upgrade: Idle -> Proposed (proposeUpgrade) -> 48h wait -> Executed (executeUpgrade) or Cancelled. timelockDuration monotonic, min 48h](../_design/23-hook-upgrade-state.svg)

- `proposeUpgrade(newImpl)` -> `readyAt = now + timelockDuration` (min 48h).
- Wait >= timelockDuration -> `executeUpgrade(newImpl, sig, readyAt)`.
- `timelockDuration` can **only increase** (monotonic), never decrease below 48h.

## Exchange (CLOB)

**Contract**: `PrediXExchange`.

### Order struct (packed)

```solidity
struct Order {
  address owner;       // 20 bytes
  uint40 timestamp;    // 5 bytes
  uint8 side;          // BUY_YES/SELL_YES/BUY_NO/SELL_NO
  bool cancelled;
  bytes32 marketId;
  uint32 price;        // fixed-point 6 decimals, range 10_000-990_000
  uint128 amount;
  uint128 filled;
  uint256 depositLocked;
}
```

### Entry points

- `placeOrder(order)` + auto-match loop
- `cancelOrder(orderId)` — owner only
- `fillMarketOrder(marketId, side, amountIn, maxFills)` — permissionless, `taker == msg.sender` gate

### 3 match types

- **Complementary**: BUY_YES <-> SELL_YES within the same market.
- **Mint** (synthetic): BUY_YES + BUY_NO >= $1. Diamond mints the pair, delivers YES to the YES buyer, NO to the NO buyer.
- **Merge** (synthetic): SELL_YES + SELL_NO <= $1. Diamond collects + burns, returns USDC.

Shared math library `MatchMath` ensures preview/execute 1-wei parity.

## Router (stateless aggregator)

**Contract**: `PrediXRouter`. Invariant: `balanceOf(router) == 0` after every public call.

### Entry points (exact-in)

```solidity
buyYes(marketId, usdcIn, minYesOut, recipient, maxFills, deadline)
sellYes(marketId, yesIn, minUsdcOut, ...)
buyNo(...)
sellNo(...)
```

### Waterfall

1. Pull USDC from Permit2.
2. **CLOB leg**: `exchange.fillMarketOrder(...)` — attempt to fill against limit orders.
   - CLOB reverts -> emit `ClobSkipped(reason)` event, fall back to full AMM.
3. **AMM leg**: `hook.commitSwapIdentityFor(...)` -> `poolManager.swap(...)` -> `unlockCallback(...)` extracts amount.
4. **Virtual-NO two-pass**: if pool lacks depth -> reduce size with 3% safety margin.
5. **Cleanup**: refund dust, assert router balance = 0 (reverts with `FinalizeBalanceNonZero` if violated).

## Oracle (overview)

**Contracts**: `ManualOracle` + `ChainlinkOracle`. Plugin architecture — adding a new oracle = deploy an adapter, `approveOracle(addr)`. Per-source detail: [Oracle](oracle.md).

## Paymaster (ERC-4337)

**Contract**: `PrediXPaymaster`. Sponsors gas via EntryPoint v0.7.

- Owner = Gnosis Safe 2-of-3 (mainnet).
- Off-chain signer (BE) signs to verify UserOp eligibility.
- Policy: sponsor for users eligible through the program.

## Quality gates

- **Compile**: `forge build`, EVM cancun, `via_ir=true`, `optimizer_runs=200`, `bytecode_hash=none`.
- **Test**: `forge test` — unit + fuzz + invariant.
- **7 critical invariants** (details in [Security & timelock](security.md)).
- **Format**: `forge fmt --check`.
- **Static analysis**: Slither with 0 critical findings.

## Upgrade model

| Component | Mechanism | Delay |
|---|---|---|
| Diamond facets | `diamondCut` via `CUT_EXECUTOR_ROLE` (TimelockController) | 48h |
| Hook implementation | `propose/executeUpgrade` via ERC1967 proxy | 48h monotonic |
| Oracle adapter | `approveOracle` instant (add), `revokeOracle` instant (remove for new markets) | 0h |
| Exchange / Router | **Immutable**. Deploy new, migrate off-chain | N/A |

Exchange and Router have no proxy. Changes require redeployment + migration (one-time event). Trade-off: simpler + immutable compared to proxy.

---

## Contract addresses

PrediX is currently in **testnet beta** (deployed 2026-04-20). Mainnet will deploy after external audit completion — addresses will be populated when available.

### Core PrediX

| Contract | Testnet (Unichain Sepolia, chain `1301`) | Mainnet (Unichain, chain `130`) |
|---|---|---|
| **Diamond** | `0x7689E9bf4b2107E2Fd0f1DDA940E2f1143434E39` | TBA |
| **Hook (proxy)** | `0x89830AC92Ff936f39C2D11D1fd821c6f977fAAE0` | TBA |
| **Hook (impl)** | `0x0dcB4624588316d9a8Dd7868EeFBF07532c29E02` | TBA |
| **Exchange** | `0xE425698e1835DA0A6086eEB85137A36275993F41` | TBA |
| **Router** | `0x6698253F38F4A4bbBC4A223309B4E560d83D7ee0` | TBA |
| **ManualOracle** | `0x7887f07AF62CE0a4Cf836136135a61b59c36A9d2` | TBA |
| **ChainlinkOracle** | Disabled (no feed on Sepolia) | TBA |
| **TimelockController** | `0x578D2a308BB0aa5d30E6BC08A7975ccA7e88af61` | TBA |
| **Paymaster** | `0x1637a7eB463b1b12906feF71eF23B76181340Cb7` | TBA |
| **Faucet (relayed)** | `0x7beD6B3D8397Bc9F77626f84D64BED8894C27350` | N/A |

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
| **PRX token** | Not deployed | TBA (mint after TGE) |
| **Staking Vault** | Not deployed | TBA |
| **vePRX** | Not deployed | TBA |
| **Uniswap v4 PoolManager** | `0x00b036b58a818b1bc34d502d3fe730db729e62ac` | TBA |
| **Permit2** | `0x000000000022D473030F116dDEE9F6B43aC78BA3` | `0x000000000022D473030F116dDEE9F6B43aC78BA3` |
| **EntryPoint v0.7** | `0x0000000071727De22E5E9d8BAf0edAc6f37da032` | `0x0000000071727De22E5E9d8BAf0edAc6f37da032` |

> Permit2 + EntryPoint are canonical addresses — identical across all chains.

### Chain config

| | Testnet | Mainnet |
|---|---|---|
| **Chain ID** | `1301` | `130` |
| **RPC public** | `https://sepolia.unichain.org` | `https://mainnet.unichain.org` |
| **Explorer** | [sepolia.uniscan.xyz](https://sepolia.uniscan.xyz) | [uniscan.xyz](https://uniscan.xyz) |
| **Block time** | ~1s | ~1s |
| **Finality** | ~12-15 min (L2) | ~12-15 min (L2) |

### Governance addresses

> **Testnet shortcut**: All testnet roles are collapsed into a single EOA for rapid dev testing. Mainnet will have properly separated Gnosis Safe multisigs.

| Role | Testnet (single EOA collapse) | Mainnet setup |
|---|---|---|
| **MULTISIG_ADMIN** | `0x0eC2bFb36BB59C736d7b770eacaFAa43a184De34` | Gnosis Safe 2-of-3, addr TBA |
| **OPERATOR** | same | Gnosis Safe 2-of-3, addr TBA |
| **PAUSER** | same | Gnosis Safe 2-of-3, addr TBA |
| **REPORTER** (oracle) | same | Multisig 2/3, addr TBA |
| **REGISTRAR** | same | Admin multisig, addr TBA |
| **FEE_RECIPIENT** | same | Treasury Safe, addr TBA |
| **CUT_EXECUTOR** | `0x578D2a308BB0aa5d30E6BC08A7975ccA7e88af61` (TimelockController testnet) | TimelockController mainnet, addr TBA, delay 48h |

### Sync with code

Apps + SDKs synchronize addresses from an on-chain registry. Do not hardcode in client code.

```javascript
const addresses = await fetch('https://api.predix.app/v2/addresses?chain=mainnet').then(r => r.json());
// or chain=testnet for Unichain Sepolia
```

### Verify source code

All contract sources are verified on Uniscan:

```
https://sepolia.uniscan.xyz/address/<ADDRESS>#code   # testnet
https://uniscan.xyz/address/<ADDRESS>#code            # mainnet (after deploy)
```

### ABI files

ABIs are publicly available at:
- npm: `@predix/abi` (TBA)
- GitHub: `github.com/predix-protocol/abi`
- Direct: `https://api.predix.app/v2/abi/<contract>`

Integration details: [Router integration](router-integration.md).

### Multi-chain (TBA)

Phase 3 — multi-chain deployment (Base / Arbitrum / Optimism / Polygon) via bridge (Wormhole / LayerZero). Details in the roadmap.

For pre-mainnet integration testing, see [Testnet info](testnet.md).
