---
description: Tất cả sự kiện giao thức với chữ ký và ví dụ lắng nghe
---

# Sự kiện

## Sự kiện MarketFacet

```solidity
event MarketCreated(bytes32 indexed marketId, string question, string description, uint256 endTime, address yesToken, address noToken, PoolId poolId, bytes32 categoryId, address oracle);
event CategoryCreated(bytes32 indexed categoryId, string name, string description, uint256 endTime, address oracle);
event MarketAddedToCategory(bytes32 indexed categoryId, bytes32 indexed marketId);
event MarketRemovedFromCategory(bytes32 indexed categoryId, bytes32 indexed marketId);
event PositionSplit(address indexed user, bytes32 indexed marketId, uint256 amount);
event PositionMerged(address indexed user, bytes32 indexed marketId, uint256 amount);
event TokensRedeemed(bytes32 indexed marketId, address indexed user, uint256 amount, uint256 payout);
event MarketResolved(bytes32 indexed marketId, uint256 outcome);
event CategoryResolved(bytes32 indexed categoryId, uint256 winningOutcomeIndex);
event GroupSplit(bytes32 indexed categoryId, address indexed user, uint256 amount);
event GroupMerge(bytes32 indexed categoryId, address indexed user, uint256 amount);
event RefundModeEnabled(bytes32 indexed marketId);
event MarketRefunded(bytes32 indexed marketId, address indexed user, uint256 amount);
```

## Sự kiện Exchange

```solidity
event OrderPlaced(bytes32 indexed orderId, bytes32 indexed marketId, address indexed owner, Side side, uint256 price, uint256 amount);
event OrderMatched(bytes32 indexed makerOrderId, bytes32 indexed takerOrderId, bytes32 indexed marketId, MatchType matchType, uint256 amount, uint256 price);
event OrderCancelled(bytes32 indexed orderId);
event FeeCollected(bytes32 indexed marketId, uint256 amount);
```

## Sự kiện Router

```solidity
event Trade(bytes32 indexed marketId, address indexed trader, uint8 tradeType, uint256 amountIn, uint256 amountOut, uint256 yesPrice);
```

## Sự kiện Hook

```solidity
event PoolRegistered(bytes32 indexed marketId, PoolId indexed poolId, address yesToken, address usdcToken);
event MarketTraded(bytes32 indexed marketId, address indexed trader, bool isBuy, uint256 amount, uint256 cost, uint8 side, uint256 yesPrice);
```

## Ví dụ lắng nghe

```typescript
// Lắng nghe giao dịch mới
router.on("Trade", (marketId, trader, tradeType, amountIn, amountOut, yesPrice) => {
  console.log(`Giao dịch trên ${marketId}:`);
  console.log(`  Nhà giao dịch: ${trader}`);
  console.log(`  Đầu vào: ${ethers.formatUnits(amountIn, 6)}`);
  console.log(`  Đầu ra: ${ethers.formatUnits(amountOut, 6)}`);
  console.log(`  Giá YES: $${ethers.formatUnits(yesPrice, 6)}`);
});

// Truy vấn sự kiện lịch sử
const filter = diamond.filters.MarketCreated();
const events = await diamond.queryFilter(filter, -10000); // 10K khối gần nhất
```

---

**Tiếp theo**: [Lỗi](errors.md) · [Tổng quan hợp đồng](../contracts/overview.md)
