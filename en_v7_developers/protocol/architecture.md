# Architecture & contracts

Solidity `0.8.34`, Foundry, EVM cancun (EIP-1153 transient storage). 7 packages, monorepo. Deployed addresses (beta + production TBA) at the bottom of this page.

## Dependency graph

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

<sub>SC dependency: shared -> oracle/diamond -> hook/exchange -> router. Cross-package import only via @predix/shared/interfaces</sub>

Rule: cross-package imports are **only** allowed via `@predix/shared/interfaces/`. No importing implementations from other packages.

## Diamond (EIP-2535)

Single proxy `PrediX Diamond` with 6 facets. Each facet is independently upgradeable.

| Facet                  | Responsibility                                                                                             |
| ---------------------- | ---------------------------------------------------------------------------------------------------------- |
| **MarketFacet**        | createMarket - split - merge - resolve - redeem - emergencyResolve - refundMode - sweep                    |
| **EventFacet**         | createEvent - resolveEvent - groupSplit - groupMerge - refundMode event                                    |
| **AccessControlFacet** | grantRole - revokeRole - 6 roles: DEFAULT\_ADMIN - OPERATOR - PAUSER - CUT\_EXECUTOR - CREATOR - REGISTRAR |
| **PausableFacet**      | pause(module) - unpause(module) - per-module pause: MARKET - DIAMOND                                       |
| **DiamondCutFacet**    | diamondCut - add/replace/remove facets, gated by `CUT_EXECUTOR_ROLE` via TimelockController 48h            |
| **DiamondLoupeFacet**  | facets() - facetAddresses() - facetFunctionSelectors() - introspection                                     |

**Storage**: Diamond storage pattern. Each facet has a `Layout` struct at slot `keccak256("predix.storage.<module>")`. **Append-only** - no reordering or removing fields.

## Hook (Uniswap v4)

**Contract**: `PrediXHookV2` (implementation) + `PrediXHookProxyV2` (ERC1967 proxy).

**Callbacks** set via permissions flag in the hook address (salt-mined):

| Callback                | Responsibility                                                     |
| ----------------------- | ------------------------------------------------------------------ |
| `beforeInitialize`      | Set permission flag + init pool state                              |
| `beforeAddLiquidity`    | Block adding LP if market is resolved / refunded                   |
| `beforeRemoveLiquidity` | Block LP modification on resolved/refunded markets                 |
| `beforeSwap`            | Verify anti-sandwich identity (EIP-1153 transient storage)         |
| `afterSwap`             | Enforce post-swap price band check + emit Hook\_MarketTraded event |
| `beforeDonate`          | Block donate after endTime (prevents brute-force payout attack)    |

**Key functions**:

* `registerMarketPool(marketId, poolKey, yesIsCurrency0)` - binds market to v4 pool, verifies canonical PoolKey (lpFee + tickSpacing match)
* `commitSwapIdentityFor(...)` - Router commits identity before swap, Hook verifies in `beforeSwap`
* `proposeTrustedRouter` / `executeTrustedRouter` - 2-step Router rotation (48h timelock)

### Hook proxy upgrade - 48h monotonic timelock

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

<sub>Hook proxy upgrade: Idle -> Proposed (proposeUpgrade) -> 48h wait -> Executed (executeUpgrade) or Cancelled. timelockDuration monotonic, min 48h</sub>

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
* `cancelOrder(orderId)` - owner only
* `fillMarketOrder(marketId, side, amountIn, maxFills)` - permissionless, `taker == msg.sender` gate

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
2. **CLOB leg**: `exchange.fillMarketOrder(...)` - attempt to fill against limit orders.
   * CLOB reverts -> emit `ClobSkipped(reason)` event, fall back to full AMM.
3. **AMM leg**: `hook.commitSwapIdentityFor(...)` -> `poolManager.swap(...)` -> `unlockCallback(...)` extracts amount.
4. **Virtual-NO two-pass**: if pool lacks depth -> reduce size with 3% safety margin.
5. **Cleanup**: refund dust, assert router balance = 0 (reverts with `FinalizeBalanceNonZero` if violated).

## Oracle (overview)

**Contracts**: `ManualOracle` + `ChainlinkOracle`. Plugin architecture - adding a new oracle = deploy an adapter, `approveOracle(addr)`. Per-source detail: [Oracle](oracle.md).

## Paymaster (ERC-4337)

**Contract**: `PrediXPaymaster`. Sponsors gas via EntryPoint v0.7.

* Owner = Safe multisig (team Safe in beta; four-Safe layout in production).
* Off-chain signer (BE) signs to verify UserOp eligibility.
* Policy: sponsor for users eligible through the program.

## Quality gates

* **Compile**: `forge build`, EVM cancun, `via_ir=true`, `optimizer_runs=200`, `bytecode_hash=none`.
* **Test**: `forge test` - unit + fuzz + invariant.
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

PrediX is currently in **beta on Unichain mainnet** (chain `130`). Production mainnet will deploy behind the full four-Safe governance layout - addresses will be populated when production launches.

### Core PrediX

| Contract             | Beta (Unichain mainnet, chain `130`)         | Production (Unichain, chain `130`) |
| -------------------- | -------------------------------------------- | ---------------------------------- |
| **Diamond**          | `0xC8F12AF2a396c9C906ac36Bc0AC2279BBb69Ef96` | TBA                                |
| **Hook**             | `0x2EA5EaC8A4E31F0889e86fc135C5eAE8e0b16AE0` | TBA                                |
| **Exchange**         | `0x506367C7c48C95A4843F45d5C2F177B35e69594E` | TBA                                |
| **Router**           | `0xf7D11488B6B0DAc511aE637AB02876dbE36cAdD6` | TBA                                |
| **ManualOracle**     | `0x8EDD86CC637FA1ca178ac16f85b6777F05AC0ca7` | TBA                                |
| **ChainlinkOracle**  | `0x87c425523Bc2890Ec624D39492CdBbBd9E74eaA2` | TBA                                |
| **Paymaster**        | `0x569ff8c104dc03651777a8c85b7f98722ab68135` | TBA                                |
| **Faucet (relayed)** | `0x335166bb5cf402b02b58904de5b8bf18c92ba10e` | N/A                                |

### Diamond facets

Facets are internal to the Diamond proxy. Developers interact with Diamond via the proxy address - individual facet addresses are not needed for integration.

### External / infrastructure

| Contract                   | Beta                                                    | Production                                                 |
| -------------------------- | ------------------------------------------------------- | ---------------------------------------------------------- |
| **USDC**                   | `0xB3FCA863dD0F6b496cCDDf6497Da5Dad67857F56` (TestUSDC) | `0x078D782b760474a361dDA0AF3839290b0EF57AD6` (Circle USDC) |
| **PRX token**              | Not deployed                                            | TBA (mint after TGE)                                       |
| **Staking Vault**          | Not deployed                                            | TBA                                                        |
| **vePRX**                  | Not deployed                                            | TBA                                                        |
| **Uniswap v4 PoolManager** | `0x1F98400000000000000000000000000000000004`            | `0x1F98400000000000000000000000000000000004`               |
| **Permit2**                | `0x000000000022D473030F116dDEE9F6B43aC78BA3`            | `0x000000000022D473030F116dDEE9F6B43aC78BA3`               |
| **EntryPoint v0.7**        | `0x0000000071727De22E5E9d8BAf0edAc6f37da032`            | `0x0000000071727De22E5E9d8BAf0edAc6f37da032`               |

> Permit2, EntryPoint, and the Uniswap v4 PoolManager are canonical addresses - identical across beta and production since both run on Unichain mainnet (chain `130`).

### Chain config

|                | Beta                               | Production                         |
| -------------- | ---------------------------------- | ---------------------------------- |
| **Chain ID**   | `130`                              | `130`                              |
| **RPC public** | `https://mainnet.unichain.org`     | `https://mainnet.unichain.org`     |
| **Explorer**   | [uniscan.xyz](https://uniscan.xyz) | [uniscan.xyz](https://uniscan.xyz) |
| **Block time** | \~1s                               | \~1s                               |
| **Finality**   | \~12-15 min (L2)                   | \~12-15 min (L2)                   |

### Governance

Protocol governance uses role-based access control via the Diamond. All admin operations go through a timelock (1h on beta, 48h+ in production). Beta uses a single team Safe for every admin role except PAUSER (a separate hot wallet for fast emergency response); production will rotate roles into a four-Safe layout per the protocol's key management policy.

### Sync with code

Contract addresses are available from verified source on the block explorer. Do not hardcode in client code - refer to the address table above or the explorer links below.

### Verify source code

All contract sources are verified on Uniscan:

```
https://uniscan.xyz/address/<ADDRESS>#code
```

### ABI files

ABI files can be obtained from verified contract source code on [Uniscan explorer](https://uniscan.xyz).

Integration details: [Router integration](../integration/router-integration.md).

### Multi-chain (TBA)

Phase 3 - multi-chain deployment (Base / Arbitrum / Optimism / Polygon) via bridge (Wormhole / LayerZero). Details in the roadmap.

For beta integration testing, see [Beta info](../getting-started/beta.md).
