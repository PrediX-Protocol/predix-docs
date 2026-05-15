# Architecture & contracts

Solidity `0.8.34`, Foundry, EVM cancun (EIP-1153 transient storage). 7 packages, monorepo. Deployed addresses (testnet + mainnet TBA) at the bottom of this page.

## Dependency graph

![SC dependency: shared -> oracle/diamond -> hook/exchange -> router. Cross-package import only via @predix/shared/interfaces](../.gitbook/assets/08-sc-dependency.svg)

Rule: cross-package imports are **only** allowed via `@predix/shared/interfaces/`. No importing implementations from other packages.

## Diamond (EIP-2535)

Single proxy `PrediX Diamond` with 6 facets. Each facet is independently upgradeable.

| Facet                  | Responsibility                                                                                             |
| ---------------------- | ---------------------------------------------------------------------------------------------------------- |
| **MarketFacet**        | createMarket - split - merge - resolve - redeem - emergencyResolve - refundMode - sweep                    |
| **EventFacet**         | createEvent - resolveEvent - groupSplit - groupMerge - refundMode event                                    |
| **AccessControlFacet** | grantRole - revokeRole - 6 roles: DEFAULT\_ADMIN - OPERATOR - PAUSER - CUT\_EXECUTOR - CREATOR - REGISTRAR |
| **PausableFacet**      | pause(module) - unpause(module) — per-module pause: MARKET - DIAMOND                                       |
| **DiamondCutFacet**    | diamondCut — add/replace/remove facets, gated by `CUT_EXECUTOR_ROLE` via TimelockController 48h            |
| **DiamondLoupeFacet**  | facets() - facetAddresses() - facetFunctionSelectors() — introspection                                     |

**Storage**: Diamond storage pattern. Each facet has a `Layout` struct at slot `keccak256("predix.storage.<module>")`. **Append-only** — no reordering or removing fields.

## Hook (Uniswap v4)

**Contract**: `PrediXHookV2` (implementation) + `PrediXHookProxyV2` (ERC1967 proxy).

**Callbacks** set via permissions flag in the hook address (salt-mined):

| Callback                | Responsibility                                                  |
| ----------------------- | --------------------------------------------------------------- |
| `beforeInitialize`      | Set permission flag + init pool state                           |
| `beforeAddLiquidity`    | Block adding LP if market is resolved / refunded                |
| `beforeRemoveLiquidity` | Block LP modification on resolved/refunded markets              |
| `beforeSwap`            | Verify anti-sandwich identity (EIP-1153 transient storage)      |
| `afterSwap`             | Enforce post-swap price band check + emit Hook_MarketTraded event |
| `beforeDonate`          | Block donate after endTime (prevents brute-force payout attack) |

**Key functions**:

* `registerMarketPool(marketId, poolKey, yesIsCurrency0)` — binds market to v4 pool, verifies canonical PoolKey (lpFee + tickSpacing match)
* `commitSwapIdentityFor(...)` — Router commits identity before swap, Hook verifies in `beforeSwap`
* `proposeTrustedRouter` / `executeTrustedRouter` — 2-step Router rotation (48h timelock)

### Hook proxy upgrade — 48h monotonic timelock

![Hook proxy upgrade: Idle -> Proposed (proposeUpgrade) -> 48h wait -> Executed (executeUpgrade) or Cancelled. timelockDuration monotonic, min 48h](../.gitbook/assets/23-hook-upgrade-state.svg)

* `proposeUpgrade(newImpl)` -> `readyAt = now + timelockDuration` (min 48h).
* Wait >= timelockDuration -> `executeUpgrade(newImpl, sig, readyAt)`.
* `timelockDuration` can **only increase** (monotonic), never decrease below 48h.

## Exchange (CLOB)

**Contract**: `PrediXExchange`.

### Order struct (packed)

```solidity
struct Order {
  address owner;       // 20 bytes
  uint40 timestamp;    // 5 bytes
  uint8 side;          // BUY_YES/SELL_YES/BUY_NO/SELL_NO
  bool cancelled;
  uint256 marketId;
  uint32 price;        // fixed-point 6 decimals, range 10_000-990_000
  uint128 amount;
  uint128 filled;
  uint256 depositLocked;
}
```

### Entry points

* `placeOrder(order)` + auto-match loop
* `cancelOrder(orderId)` — owner only
* `fillMarketOrder(marketId, side, amountIn, maxFills)` — permissionless, `taker == msg.sender` gate

### 3 match types

* **Complementary**: BUY\_YES <-> SELL\_YES within the same market.
* **Mint** (synthetic): BUY\_YES + BUY\_NO >= $1. Diamond mints the pair, delivers YES to the YES buyer, NO to the NO buyer.
* **Merge** (synthetic): SELL\_YES + SELL\_NO <= $1. Diamond collects + burns, returns USDC.

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
   * CLOB reverts -> emit `ClobSkipped(reason)` event, fall back to full AMM.
3. **AMM leg**: `hook.commitSwapIdentityFor(...)` -> `poolManager.swap(...)` -> `unlockCallback(...)` extracts amount.
4. **Virtual-NO two-pass**: if pool lacks depth -> reduce size with 3% safety margin.
5. **Cleanup**: refund dust, assert router balance = 0 (reverts with `FinalizeBalanceNonZero` if violated).

## Oracle (overview)

**Contracts**: `ManualOracle` + `ChainlinkOracle`. Plugin architecture — adding a new oracle = deploy an adapter, `approveOracle(addr)`. Per-source detail: [Oracle](oracle.md).

## Paymaster (ERC-4337)

**Contract**: `PrediXPaymaster`. Sponsors gas via EntryPoint v0.7.

* Owner = Gnosis Safe 2-of-3 (mainnet).
* Off-chain signer (BE) signs to verify UserOp eligibility.
* Policy: sponsor for users eligible through the program.

## Quality gates

* **Compile**: `forge build`, EVM cancun, `via_ir=true`, `optimizer_runs=200`, `bytecode_hash=none`.
* **Test**: `forge test` — unit + fuzz + invariant.
* **7 critical invariants** (details in [Security & timelock](security.md)).
* **Format**: `forge fmt --check`.
* **Static analysis**: Slither with 0 critical findings.

## Upgrade model

| Component           | Mechanism                                                                      | Delay         |
| ------------------- | ------------------------------------------------------------------------------ | ------------- |
| Diamond facets      | `diamondCut` via `CUT_EXECUTOR_ROLE` (TimelockController)                      | 48h           |
| Hook implementation | `propose/executeUpgrade` via ERC1967 proxy                                     | 48h monotonic |
| Oracle adapter      | `approveOracle` instant (add), `revokeOracle` instant (remove for new markets) | 0h            |
| Exchange / Router   | **Immutable**. Deploy new, migrate off-chain                                   | N/A           |

Exchange and Router have no proxy. Changes require redeployment + migration (one-time event). Trade-off: simpler + immutable compared to proxy.

***

## Contract addresses

PrediX is currently in **testnet beta** (deployed 2026-04-20). Mainnet will deploy after external audit completion — addresses will be populated when available.

### Core PrediX

| Contract               | Testnet (Unichain Sepolia, chain `1301`)     | Mainnet (Unichain, chain `130`) |
| ---------------------- | -------------------------------------------- | ------------------------------- |
| **Diamond**            | `0xa7a35F11e184Bde540702083160647518f5Be302` | TBA                             |
| **Hook**               | `0xc167a6bD746a5a884b3C0546B0115D0FdC04aAe0` | TBA                             |
| **Exchange**           | `0x95a5Db0694c7C185b152E24b7d58D527af236b85` | TBA                             |
| **Router**             | `0x1267723f500C0437295698d36d521bd060Bed0EB` | TBA                             |
| **ManualOracle**       | `0x9ffbf61f9481D71BB6F40e1955F4096De4c52cF6` | TBA                             |
| **ChainlinkOracle**    | Disabled (no feed on Sepolia)                | TBA                             |
| **Paymaster**          | `0x6bBeeb1255a25e6a57b87D9d88fBE24c3a1Ba9e7` | TBA                             |
| **Faucet (relayed)**   | `0x76C951B6185A2B44e44c98E7A0E9Ee59b08760da` | N/A                             |

### Diamond facets

Facets are internal to the Diamond proxy. Developers interact with Diamond via the proxy address — individual facet addresses are not needed for integration.

### External / infrastructure

| Contract                   | Testnet                                                  | Mainnet                                      |
| -------------------------- | -------------------------------------------------------- | -------------------------------------------- |
| **USDC**                   | `0x5a9153c368946B5b252c32921EbB3c16c692D7D4` (test-USDC) | TBA                                          |
| **PRX token**              | Not deployed                                             | TBA (mint after TGE)                         |
| **Staking Vault**          | Not deployed                                             | TBA                                          |
| **vePRX**                  | Not deployed                                             | TBA                                          |
| **Uniswap v4 PoolManager** | `0x00b036b58a818b1bc34d502d3fe730db729e62ac`             | TBA                                          |
| **Permit2**                | `0x000000000022D473030F116dDEE9F6B43aC78BA3`             | `0x000000000022D473030F116dDEE9F6B43aC78BA3` |
| **EntryPoint v0.7**        | `0x0000000071727De22E5E9d8BAf0edAc6f37da032`             | `0x0000000071727De22E5E9d8BAf0edAc6f37da032` |

> Permit2 + EntryPoint are canonical addresses — identical across all chains.

### Chain config

|                | Testnet                                            | Mainnet                            |
| -------------- | -------------------------------------------------- | ---------------------------------- |
| **Chain ID**   | `1301`                                             | `130`                              |
| **RPC public** | `https://sepolia.unichain.org`                     | `https://mainnet.unichain.org`     |
| **Explorer**   | [sepolia.uniscan.xyz](https://sepolia.uniscan.xyz) | [uniscan.xyz](https://uniscan.xyz) |
| **Block time** | \~1s                                               | \~1s                               |
| **Finality**   | \~12-15 min (L2)                                   | \~12-15 min (L2)                   |

### Governance

Protocol governance uses role-based access control via the Diamond. All admin operations go through a 48h timelock. Mainnet will use Gnosis Safe 2-of-3 multisig for all governance roles.

### Sync with code

Contract addresses are available from verified source on the block explorer. Do not hardcode in client code — refer to the address table above or the explorer links below.

### Verify source code

All contract sources are verified on Uniscan:

```
https://sepolia.uniscan.xyz/address/<ADDRESS>#code   # testnet
https://uniscan.xyz/address/<ADDRESS>#code            # mainnet (after deploy)
```

### ABI files

ABI files can be obtained from verified contract source code on [Uniscan explorer](https://sepolia.uniscan.xyz).

Integration details: [Router integration](router-integration.md).

### Multi-chain (TBA)

Phase 3 — multi-chain deployment (Base / Arbitrum / Optimism / Polygon) via bridge (Wormhole / LayerZero). Details in the roadmap.

For pre-mainnet integration testing, see [Testnet info](testnet.md).
