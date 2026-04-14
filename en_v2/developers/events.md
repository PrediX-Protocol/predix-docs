# Events

All on-chain events emitted by PrediX contracts. Use these for indexing, notifications, and real-time UI updates.

## Diamond (MarketFacet) Events

```solidity
MarketCreated(bytes32 indexed marketId, string question, string description, uint256 endTime, address yesToken, address noToken, PoolId poolId, bytes32 categoryId, address oracle)
CategoryCreated(bytes32 indexed categoryId, string name, string description, uint256 endTime, address oracle)
MarketAddedToCategory(bytes32 indexed categoryId, bytes32 indexed marketId)
MarketRemovedFromCategory(bytes32 indexed categoryId, bytes32 indexed marketId)
PositionSplit(address indexed user, bytes32 indexed marketId, uint256 amount)
PositionMerged(address indexed user, bytes32 indexed marketId, uint256 amount)
TokensRedeemed(bytes32 indexed marketId, address indexed user, uint256 amount, uint256 payout)
MarketResolved(bytes32 indexed marketId, uint256 outcome)
CategoryResolved(bytes32 indexed categoryId, uint256 winningOutcomeIndex)
GroupSplit(bytes32 indexed categoryId, address indexed user, uint256 amount)
GroupMerge(bytes32 indexed categoryId, address indexed user, uint256 amount)
RefundModeEnabled(bytes32 indexed marketId)
MarketRefunded(bytes32 indexed marketId, address indexed user, uint256 amount)
```

## Exchange Events

```solidity
OrderPlaced(bytes32 indexed orderId, bytes32 indexed marketId, address indexed owner, Side side, uint256 price, uint256 amount)
OrderMatched(bytes32 indexed makerOrderId, bytes32 indexed takerOrderId, bytes32 indexed marketId, MatchType matchType, uint256 amount, uint256 price)
OrderCancelled(bytes32 indexed orderId)
FeeCollected(bytes32 indexed marketId, uint256 amount)
```

## Router Events

```solidity
Trade(bytes32 indexed marketId, address indexed trader, uint8 tradeType, uint256 amountIn, uint256 amountOut, uint256 yesPrice)
```

## Hook Events

```solidity
PoolRegistered(bytes32 indexed marketId, PoolId indexed poolId, address yesToken, address usdcToken)
MarketTraded(bytes32 indexed marketId, address indexed trader, bool isBuy, uint256 amount, uint256 cost, uint8 side, uint256 yesPrice)
```

## Listening for Events

```typescript
const diamond = new ethers.Contract(DIAMOND, [
  "event MarketCreated(bytes32 indexed marketId, string question, string description, uint256 endTime, address yesToken, address noToken, bytes32 poolId, bytes32 categoryId, address oracle)",
  "event Trade(bytes32 indexed marketId, address indexed trader, uint8 tradeType, uint256 amountIn, uint256 amountOut, uint256 yesPrice)"
], provider);

// Listen for new markets
diamond.on("MarketCreated", (marketId, question, description, endTime) => {
  console.log(`New market: ${question} (${marketId})`);
});

// Listen for trades on a specific market
const filter = diamond.filters.Trade(marketId);
diamond.on(filter, (mktId, trader, tradeType, amountIn, amountOut, yesPrice) => {
  console.log(`Trade: ${ethers.formatUnits(amountIn, 6)} in → ${ethers.formatUnits(amountOut, 6)} out`);
});
```

## Indexing Recommendations

| Tool | Best For |
|------|----------|
| **Ponder** | TypeScript-first, fast iteration, local dev |
| **The Graph** | Production subgraphs, decentralized querying |
| **Envio** | High-performance, multi-chain indexing |

## Next Steps

- [Errors](errors.md) — custom error reference
- [Contracts Overview](../contracts/overview.md) — contract addresses
