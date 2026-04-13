---
description: Diamond proxy (EIP-2535) — market management, split, merge, resolve, redeem
---

# Diamond Contract

The Diamond is the central contract of PrediX Protocol, implementing EIP-2535 for modular upgradeability.

## Facets

| Facet | Functions |
| ----- | --------- |
| **DiamondCutFacet** | `diamondCut` — add/replace/remove facets |
| **DiamondLoupeFacet** | `facets`, `facetAddresses`, `facetFunctionSelectors` — introspection |
| **AccessControlFacet** | `grantRole`, `revokeRole`, `hasRole` |
| **OwnableFacet** | `transferOwnership`, `acceptOwnership`, `owner` |
| **PausableFacet** | `pause`, `unpause`, `paused` |
| **MarketFacet** | All market operations (see below) |

## How It Works

Every function call to the Diamond is routed by selector:

```
User calls diamond.splitPosition(marketId, amount)
  → Diamond fallback: lookup selector 0x... → MarketFacet address
  → delegatecall to MarketFacet
  → MarketFacet executes using Diamond's storage
```

## MarketFacet Functions

### Creation (ADMIN_ROLE)

```solidity
createMarket(string question, string description, uint256 endTime,
             bytes32 categoryId, address oracle) → bytes32 marketId

createCategory(string name, string description, uint256 endTime) → bytes32 categoryId

addMarketToCategory(bytes32 categoryId, bytes32 marketId)
removeMarketFromCategory(bytes32 categoryId, bytes32 marketId)
```

### Position Management (PUBLIC)

```solidity
splitPosition(bytes32 marketId, uint256 amount)
mergePositions(bytes32 marketId, uint256 amount)
groupSplit(bytes32 categoryId, uint256 amount)
groupMerge(bytes32 categoryId, uint256 amount)
```

### Resolution

```solidity
resolveMarket(bytes32 marketId)                              // PUBLIC (after oracle)
resolveCategory(bytes32 categoryId, uint256 winningIndex)    // ADMIN_ROLE
emergencyResolve(bytes32 marketId, bool outcome)             // OPERATOR_ROLE (7d delay)
enableRefundMode(bytes32 marketId)                           // OPERATOR_ROLE
refund(bytes32 marketId)                                     // PUBLIC (refund mode)
```

### Redemption (PUBLIC)

```solidity
redeemMarketTokens(bytes32 marketId)
redeemCategoryTokens(bytes32 categoryId)
```

### View Functions

```solidity
getMarket(bytes32 marketId) → MarketData
getCategory(bytes32 categoryId) → CategoryData
isMarketExists(bytes32 marketId) → bool
isRefundMode(bytes32 marketId) → bool
getSafetyCaps() → (uint256 tvlCap, uint256 perTradeCap, uint256 perMarketCap)
```

### Admin (ADMIN_ROLE)

```solidity
setApprovedOracle(address oracle, bool approved)
setTvlCap(uint256 cap)
setPerTradeCap(uint256 cap)
setPerMarketCap(uint256 cap)
```

## MarketData Struct

```solidity
struct MarketData {
    string question;
    string description;
    uint256 endTime;
    address yesToken;
    address noToken;
    PoolId poolId;
    bytes32 categoryId;
    address oracle;
    bool isResolved;
    bool outcome;           // true = YES wins, false = NO wins
    uint256 totalCollateral;
    bytes32 questionHash;   // DEPRECATED
    uint256 resolvedAt;
}
```

> ⚠️ **Critical selectors** (`diamondCut`, `owner`, `transferOwnership`) are protected and cannot be removed.

---

**Next**: [Exchange](exchange.md) · [Router](router.md) · [Access Control](access-control.md)
