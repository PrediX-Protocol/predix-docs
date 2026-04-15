# 37. Events

## Diamond (MarketFacet)
MarketCreated(bytes32 indexed marketId, string question, string description, uint256 endTime, address yesToken, address noToken, PoolId poolId, bytes32 categoryId, address oracle)
CategoryCreated(bytes32 indexed categoryId, string name, string description, uint256 endTime, address oracle)
MarketAddedToCategory(bytes32 indexed categoryId, bytes32 indexed marketId)
PositionSplit(address indexed user, bytes32 indexed marketId, uint256 amount)
PositionMerged(address indexed user, bytes32 indexed marketId, uint256 amount)
TokensRedeemed(bytes32 indexed marketId, address indexed user, uint256 amount, uint256 payout)
MarketResolved(bytes32 indexed marketId, uint256 outcome)
CategoryResolved(bytes32 indexed categoryId, uint256 winningOutcomeIndex)
RefundModeEnabled(bytes32 indexed marketId)
MarketRefunded(bytes32 indexed marketId, address indexed user, uint256 amount)

## Exchange
OrderPlaced(bytes32 indexed orderId, bytes32 indexed marketId, address indexed owner, Side side, uint256 price, uint256 amount)
OrderMatched(bytes32 indexed makerOrderId, bytes32 indexed takerOrderId, bytes32 indexed marketId, MatchType matchType, uint256 amount, uint256 price)
OrderCancelled(bytes32 indexed orderId)
FeeCollected(bytes32 indexed marketId, uint256 amount)

## Router
Trade(bytes32 indexed marketId, address indexed trader, uint8 tradeType, uint256 amountIn, uint256 amountOut, uint256 yesPrice)

## Hook
PoolRegistered(bytes32 indexed marketId, PoolId indexed poolId, address yesToken, address usdcToken)
MarketTraded(bytes32 indexed marketId, address indexed trader, bool isBuy, uint256 amount, uint256 cost, uint8 side, uint256 yesPrice)

## Listen for Events (ethers.js)
diamond.on("MarketCreated", (marketId, question, description, endTime) => {
  console.log("New market:", question);
});

exchange.on("OrderMatched", (makerOrderId, takerOrderId, marketId, matchType, amount, price) => {
  console.log("Order matched:", ethers.formatUnits(amount, 6), "at", ethers.formatUnits(price, 6));
});
