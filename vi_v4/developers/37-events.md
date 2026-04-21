---
description: Event signature reference — all contracts, với enum values và indexed fields.
---

# Events Reference

## Diamond: MarketFacet

```solidity
event MarketCreated(
    uint256 indexed marketId,
    address indexed creator,
    address indexed oracle,
    address yesToken,
    address noToken,
    uint256 endTime,
    string question
);

event PositionSplit(address indexed user, uint256 indexed marketId, uint256 amount);
event PositionMerged(address indexed user, uint256 indexed marketId, uint256 amount);

event TokensRedeemed(uint256 indexed marketId, address indexed user, uint256 amount, uint256 payout);

event MarketResolved(uint256 indexed marketId, bool outcome);
event MarketEmergencyResolved(uint256 indexed marketId, bool outcome, address indexed operator);

event RefundModeEnabled(uint256 indexed marketId);
event MarketRefunded(uint256 indexed marketId, address indexed user, uint256 amount);

event UnclaimedSwept(uint256 indexed marketId, uint256 amount, address indexed recipient);

event OracleApproved(address indexed oracle, address indexed admin);
event OracleRevoked(address indexed oracle, address indexed admin);

event FeeRecipientUpdated(address indexed oldRecipient, address indexed newRecipient);
event MarketCreationFeeUpdated(uint256 oldFee, uint256 newFee);
event DefaultPerMarketCapUpdated(uint256 oldCap, uint256 newCap);
event PerMarketCapUpdated(uint256 indexed marketId, uint256 oldCap, uint256 newCap);
event DefaultRedemptionFeeUpdated(uint16 oldFeeBps, uint16 newFeeBps);
event PerMarketRedemptionFeeUpdated(uint256 indexed marketId, uint16 oldFeeBps, uint16 newFeeBps, bool overridden);
```

## Diamond: EventFacet

```solidity
event EventCreated(
    uint256 indexed eventId,
    string name,
    uint256[] marketIds,
    uint256 endTime,
    address indexed creator,
    address indexed oracle
);

event EventResolved(uint256 indexed eventId, uint256 winningIndex, uint256 winningChildMarketId);
event EventRefundModeEnabled(uint256 indexed eventId);
```

## Diamond: AccessControl

```solidity
event RoleGranted(bytes32 indexed role, address indexed account, address indexed sender);
event RoleRevoked(bytes32 indexed role, address indexed account, address indexed sender);
event RoleAdminChanged(bytes32 indexed role, bytes32 indexed previousAdminRole, bytes32 indexed newAdminRole);
```

## Diamond: Pausable

```solidity
event ModulePaused(bytes32 indexed module, address indexed account);
event ModuleUnpaused(bytes32 indexed module, address indexed account);
```

## Diamond: DiamondCut

```solidity
event DiamondCut(FacetCut[] _diamondCut, address _init, bytes _calldata);

struct FacetCut {
    address facetAddress;
    FacetCutAction action;  // 0=Add, 1=Replace, 2=Remove
    bytes4[] functionSelectors;
}
```

## PrediXExchange

```solidity
event OrderPlaced(
    bytes32 indexed orderId,
    uint256 indexed marketId,
    address indexed owner,
    Side side,
    uint256 price,
    uint256 amount
);

event OrderMatched(
    bytes32 indexed makerOrderId,
    bytes32 indexed takerOrderId,
    uint256 indexed marketId,
    MatchType matchType,
    uint256 amount,
    uint256 price
);

event OrderCancelled(bytes32 indexed orderId);
event FeeCollected(uint256 indexed marketId, uint256 amount);
```

### Enum values

```solidity
enum Side { BUY_YES, SELL_YES, BUY_NO, SELL_NO }  // 0, 1, 2, 3
enum MatchType { COMPLEMENTARY, MINT, MERGE }      // 0, 1, 2
```

## PrediXRouter (canonical trade source)

```solidity
event Trade(
    uint256 indexed marketId,
    address indexed trader,
    address indexed recipient,
    uint8 tradeType,       // enum TradeType
    uint256 amountIn,
    uint256 amountOut,
    uint256 clobFilled,
    uint256 ammFilled,
    uint256 yesPrice
);

event DustRefunded(address indexed recipient, address token, uint256 amount);
```

### TradeType enum

```solidity
enum TradeType { BUY_YES, SELL_YES, BUY_NO, SELL_NO }  // 0, 1, 2, 3
```

## PrediXHookV2

```solidity
event Hook_PoolRegistered(uint256 indexed marketId, PoolId indexed poolId, address yesToken);

event Hook_MarketTraded(
    uint256 indexed marketId,
    address indexed trader,
    bool isBuy,
    uint256 amount,
    uint256 cost,
    uint8 side,
    uint256 yesPrice
);

event Hook_TrustedRouterUpdated(address indexed router, bool trusted);
event Hook_AdminChangeProposed(address indexed newAdmin, uint256 proposedAt);
event Hook_AdminUpdated(address indexed oldAdmin, address indexed newAdmin);
event Hook_PauseStatusChanged(bool paused);
```

## PrediXHookProxyV2

```solidity
event UpgradeProposed(address indexed newImpl, uint256 proposedAt);
event Upgraded(address indexed newImpl);
event UpgradeCancelled();

event AdminChangeProposed(address indexed newAdmin, uint256 proposedAt);
event AdminChanged(address indexed oldAdmin, address indexed newAdmin);
event TimelockDurationUpdated(uint256 oldDuration, uint256 newDuration);
event InitReverted(bytes reason);
```

## ManualOracle

```solidity
event ResolutionSet(uint256 indexed marketId, bool outcome, address indexed operator);
event AdminChanged(address indexed oldAdmin, address indexed newAdmin);
```

## ChainlinkOracle

```solidity
event MarketBound(
    uint256 indexed marketId,
    address priceFeed,
    int256 threshold,
    uint8 cmp,           // Comparison enum: GT=0, GTE=1, LT=2, LTE=3
    uint256 resolveTime
);

event Resolved(uint256 indexed marketId, int256 price, bool outcome, uint80 roundId);
```

## OutcomeToken

```solidity
// Standard ERC-20
event Transfer(address indexed from, address indexed to, uint256 value);
event Approval(address indexed owner, address indexed spender, uint256 value);
```

---

## Listen via viem

Xem [Event Listening](41-event-listening.md) cho full guide.

```typescript
publicClient.watchContractEvent({
  address: ROUTER,
  abi: RouterAbi,
  eventName: 'Trade',
  onLogs: logs => {
    for (const log of logs) {
      console.log('Trade:', log.args);
    }
  },
});
```

## Indexer mapping

Mọi event được index vào PostgreSQL tables. Xem [Events Mapping](../indexer/03-events-mapping.md) cho full SC → table mapping.

## Canonical volume source

- `Router.Trade` là **canonical** source cho `market.volume` + `protocolStats.totalTrades`
- `Hook_MarketTraded` emit cho **mọi AMM swap** (từ Router hoặc trực tiếp) — Indexer dùng chỉ cho `priceSnapshot(source=hook_amm)` analytics, **không** count volume

Bot dev cần track realtime volume → subscribe `Router.Trade`, không subscribe `Hook_MarketTraded`.
