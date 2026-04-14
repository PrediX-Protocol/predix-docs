# Diamond Contract

The Diamond proxy (EIP-2535) is the core contract managing markets, positions, and resolution. All calls are routed by function selector to the appropriate facet.

## Facets

| Facet | Responsibility |
|-------|---------------|
| **DiamondCutFacet** | Add/replace/remove facets |
| **DiamondLoupeFacet** | Introspection (list facets, selectors) |
| **AccessControlFacet** | Role management (4 roles) |
| **OwnableFacet** | Two-step ownership transfer |
| **PausableFacet** | Emergency pause per facet or global |
| **MarketFacet** | Markets, positions, resolution, redemption |

## How Function Routing Works

```
User calls Diamond.splitPosition(marketId, amount)
  → Diamond fallback checks selector 0x...
  → Routes to MarketFacet.splitPosition()
  → Executes in Diamond's storage context
```

## MarketFacet Functions

### Market Creation (ADMIN_ROLE)

```solidity
createMarket(string question, string description, uint256 endTime, bytes32 categoryId, address oracle) → bytes32 marketId
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

For split/merge mechanics, see [Split & Merge](../concepts/split-and-merge.md).

### Resolution

```solidity
resolveMarket(bytes32 marketId)                                    // PUBLIC — after endTime + oracle resolved
resolveCategory(bytes32 categoryId, uint256 winningOutcomeIndex)   // ADMIN_ROLE
emergencyResolve(bytes32 marketId, bool outcome)                   // OPERATOR_ROLE — 7d after endTime
enableRefundMode(bytes32 marketId)                                 // OPERATOR_ROLE
refund(bytes32 marketId)                                           // PUBLIC — when refundMode active
```

For resolution flow, see [Resolution](../concepts/resolution.md).

### Redemption (PUBLIC)

```solidity
redeemMarketTokens(bytes32 marketId)
redeemCategoryTokens(bytes32 categoryId)
```

### View Functions (PUBLIC)

```solidity
getMarket(bytes32 marketId) → MarketData
getCategory(bytes32 categoryId) → CategoryData
isMarketExists(bytes32 marketId) → bool
isRefundMode(bytes32 marketId) → bool
getSafetyCaps() → (tvlCap, perTradeCap, perMarketCap)
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

## Upgrade Process

Only `DEFAULT_ADMIN_ROLE` can call `diamondCut()` to add, replace, or remove facets. Each upgrade specifies an array of facet addresses and their selectors.

## Next Steps

- [Exchange](exchange.md) — CLOB order book
- [Security](security.md) — access control roles and safety caps
- [Developer: Create Market](../developers/create-market.md) — step-by-step code
