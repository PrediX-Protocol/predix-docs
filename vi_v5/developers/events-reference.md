# Events reference

Danh sách event smart contract emit, mapping sang table indexer. Dùng cho bot off-chain, subgraph, hoặc monitor.

## Source of truth

- **Canonical trade**: `PrediXRouter.Trade` — `protocolStats.totalVolume / totalTrades` **chỉ** tăng từ đây.
- **AMM swap analytics**: `PrediXHookV2.Hook_MarketTraded` — priceSnapshot analytics, KHÔNG count volume (tránh double-count với Router.Trade).
- **Audit rows luôn land**: `PositionSplit`, `PositionMerged`, `TokensRedeemed`, `MarketRefunded` — ghi bất kể user là protocol contract hay EOA.

## Router

### `Trade`

```solidity
event Trade(
    address indexed trader,
    address indexed recipient,
    bytes32 indexed marketId,
    uint8 tradeType,        // 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
    uint256 amountIn,       // USDC hoặc YES/NO
    uint256 amountOut,      // Ngược chiều amountIn
    uint256 yesPrice,       // Giá YES trung bình trade này (6 decimals)
    uint256 clobFilled,     // Amount qua CLOB
    uint256 ammFilled       // Amount qua AMM
);
```

**Indexer table**: `trade`, `position` (if recipient ≠ protocol contract), `market.volume`, `market.tradeCount`, `priceSnapshot` (source="router"), `protocolStats`, `user`, `userStats`.

### `DustRefunded`

```solidity
event DustRefunded(address indexed trader, uint256 amount);
```

No-op trong indexer — thông tin đã có trong `Trade`.

### `ClobSkipped`

```solidity
event ClobSkipped(bytes32 indexed marketId, bytes4 selector);
```

Emit khi CLOB revert, Router fall back AMM-only. `selector` là 4-byte của custom error để debug.

## Exchange

### `OrderPlaced`

```solidity
event OrderPlaced(
    uint256 indexed orderId,
    address indexed owner,
    bytes32 indexed marketId,
    uint8 side,
    uint32 price,
    uint128 amount
);
```

Table: `exchangeOrder` (status=PENDING).

### `OrderMatched`

```solidity
event OrderMatched(
    uint256 indexed takerOrderId,
    uint256 indexed makerOrderId,
    bytes32 indexed marketId,
    uint8 matchType,          // 0=COMPLEMENTARY, 1=MINT, 2=MERGE
    uint128 fillAmount,
    uint32 fillPrice
);
```

Tables: `exchangeOrder` (update filled), `orderMatch`, `takerFill`, `position` (if non-protocol).

### `OrderCancelled`

```solidity
event OrderCancelled(uint256 indexed orderId, address indexed owner);
```

Table: `exchangeOrder` (status=CANCELLED).

### `OrderFilled`

```solidity
event OrderFilled(uint256 indexed orderId);
```

Table: `exchangeOrder` (status=FILLED).

## MarketFacet

### `MarketCreated`

```solidity
event MarketCreated(
    bytes32 indexed marketId,
    address indexed creator,
    string question,
    uint256 endTime,
    address oracle,
    address yesToken,
    address noToken,
    uint256 eventId,
    uint32 redemptionFeeBps
);
```

Tables: `market`, `outcomeToken`, `protocolStats`, `user`.

### `PositionSplit` / `PositionMerged`

```solidity
event PositionSplit(
    bytes32 indexed marketId,
    address indexed user,
    uint256 amount
);

event PositionMerged(
    bytes32 indexed marketId,
    address indexed user,
    uint256 amount
);
```

Tables: `positionSplit` / `positionMerge` (ALWAYS), `position` (if !isProtocolContract), `market.totalCollateral`, `protocolStats`.

### `MarketResolved`

```solidity
event MarketResolved(
    bytes32 indexed marketId,
    bool outcome,
    uint256 resolvedAt
);
```

Tables: `market.isResolved`, `market.outcome`, `position.outcomeAtResolve`, `protocolStats`, `userStats.accuracyScore`.

### `TokensRedeemed`

```solidity
event TokensRedeemed(
    bytes32 indexed marketId,
    address indexed user,
    uint256 winningBurned,
    uint256 losingBurned,
    uint256 payout,
    uint256 fee
);
```

Table: `redemption`.

### `MarketRefunded`

```solidity
event MarketRefunded(
    bytes32 indexed marketId,
    address indexed user,
    uint256 yesBurned,
    uint256 noBurned,
    uint256 payout
);
```

Note: post FINAL-C01 fix, `yesBurned == noBurned` always (pair refund).

Table: `refundClaim`.

### `MarketEmergencyResolved`

```solidity
event MarketEmergencyResolved(
    bytes32 indexed marketId,
    bool outcome,
    address operator
);
```

Table: same as MarketResolved + emergency flag.

### `RefundModeEnabled`

```solidity
event RefundModeEnabled(bytes32 indexed marketId);
```

Table: `market.refundModeActive = true`.

### `PerMarketRedemptionFeeUpdated`

```solidity
event PerMarketRedemptionFeeUpdated(bytes32 indexed marketId, uint32 newFeeBps);
```

Tables: `market.perMarketRedemptionFeeBps`, `feeConfigChange`.

## EventFacet

```solidity
event EventGroupCreated(
    uint256 indexed eventId,
    string name,
    uint256 endTime,
    bytes32[] marketIds
);

event EventGroupResolved(
    uint256 indexed eventId,
    uint8 winningIndex
);

event EventRefundModeEnabled(uint256 indexed eventId);
```

Table: `eventGroup`.

## Hook

### `Hook_PoolRegistered`

```solidity
event Hook_PoolRegistered(
    bytes32 indexed poolId,
    bytes32 indexed marketId,
    bool yesIsCurrency0
);
```

Table: `hookPoolBinding` — essential lookup cho filter PoolManager events.

### `Hook_MarketTraded`

```solidity
event Hook_MarketTraded(
    bytes32 indexed marketId,
    address indexed trader,
    uint256 yesPrice,
    uint256 volume,
    bool isBuyYes
);
```

Table: `priceSnapshot` (source="hook_amm"), `market.lastTradeAt`. **KHÔNG** count volume — Router canonical.

### `Hook_PauseStatusChanged`

```solidity
event Hook_PauseStatusChanged(bool paused);
```

Table: `pauseEvent` scope="HOOK".

### Hook admin / upgrade

```solidity
event Hook_AdminChangeProposed(address newAdmin, uint256 readyAt);
event Hook_AdminChangeExecuted(address newAdmin);
event Hook_TrustedRouterProposed(address newRouter, uint256 readyAt);
event Hook_TrustedRouterExecuted(address newRouter);
event Upgrade_Proposed(address newImpl, uint256 readyAt);
event Upgraded(address indexed implementation);
```

Tables: `hookAdminChange`, `hookProxyUpgrade`.

## Oracle

### ManualOracle

```solidity
event OracleReportCreated(
    bytes32 indexed marketId,
    bool outcome,
    address reporter
);

event OracleReportRevoked(bytes32 indexed marketId, address operator);
```

Table: `manualOracleReport`.

### ChainlinkOracle

```solidity
event OracleMarketRegistered(
    bytes32 indexed marketId,
    address feed,
    uint256 threshold,
    bool gte,
    uint256 snapshotAt
);

event OracleMarketResolved(bytes32 indexed marketId, bool outcome);
```

Table: `chainlinkOracleMarket`.

## AccessControlFacet

```solidity
event RoleGranted(bytes32 indexed role, address indexed account, address sender);
event RoleRevoked(bytes32 indexed role, address indexed account, address sender);
```

Table: `roleChange`.

## DiamondCutFacet

```solidity
event DiamondCut(FacetCut[] cuts, address init, bytes data);
```

Table: `diamondCutLog`.

## OutcomeToken (ERC-20 factory)

```solidity
event Transfer(address indexed from, address indexed to, uint256 value);
event Approval(address indexed owner, address indexed spender, uint256 value);
```

Table: `holder` — canonical balance view. Mỗi market có 1 YES + 1 NO token, handler subscribe factory.

## PoolManager (Uniswap v4)

Handler filter by `hookPoolBinding` membership trước khi xử lý:

- `Initialize` → insert `ammPoolState`.
- `Swap` → update `sqrtPriceX96`, `liquidity`, `tick`.
- `ModifyLiquidity` → update `liquidity += delta`.

Events khác (Donate, …) → ignore.

## Usage patterns

### Listen Trade events cho market

```ts
import { getContract, createPublicClient, http, watchContractEvent } from 'viem';

const client = createPublicClient({ chain, transport: http() });

client.watchContractEvent({
  address: ROUTER,
  abi: routerAbi,
  eventName: 'Trade',
  args: { marketId },
  onLogs: (logs) => {
    logs.forEach(log => console.log(log.args));
  },
});
```

### Query historical trades

```ts
const logs = await client.getContractEvents({
  address: ROUTER,
  abi: routerAbi,
  eventName: 'Trade',
  args: { marketId },
  fromBlock: DEPLOY_BLOCK,
  toBlock: 'latest',
});
```

Hoặc dùng Indexer REST cho query history thay vì gọi RPC `getLogs` trực tiếp (limit block range).
