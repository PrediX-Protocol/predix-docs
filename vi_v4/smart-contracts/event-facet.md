---
description: EventFacet — multi-outcome events kiểu Polymarket. N binary market liên kết, mutual-exclusion resolution.
---

# EventFacet (Multi-Outcome)

`EventFacet` cho phép nhóm N binary market thành một **event group** với invariant mutual exclusion: **chỉ một child market có thể YES-wins**, các child khác tự động NO-wins.

Use case: "Ai thắng bầu cử Tổng thống Mỹ 2028?" — tạo 1 event group gồm N market con, mỗi market = 1 candidate. Khi oracle resolve, chính xác 1 market YES-wins, còn lại buộc NO-wins.

{% hint style="warning" %}
Trước đây docs gọi tính năng này là "Category" — thuật ngữ cũ đã bỏ. Canonical là **Event** / **EventFacet** trên chain.
{% endhint %}

## Cấu trúc EventGroup

```solidity
struct EventGroup {
    string name;
    uint256[] marketIds;      // N children
    uint256 endTime;
    address creator;
    bool isResolved;
    uint256 winningIndex;      // index vào marketIds[]
    uint256 winningChildMarketId;
    bool refundModeActive;
    uint256 resolvedAt;
}
```

## Admin functions

### `createEvent`

```solidity
function createEvent(
    string calldata name,
    string[] calldata childQuestions,   // 1 question per child market
    uint256 endTime,
    address oracle
) external returns (uint256 eventId, uint256[] memory marketIds);
```

**Role**: `ADMIN_ROLE`
**Effects**:
- Tạo N market con qua `MarketFacet.createMarket` internal call
- Set `market.eventId = eventId` cho mỗi child
- Return `eventId` (monotonic) + `marketIds[]`

**Event**: `EventCreated(eventId, name, marketIds, endTime)`

## User-facing

User **không** split/merge trên EventGroup trực tiếp. Thao tác trực tiếp lên từng child market qua `MarketFacet`. EventGroup chỉ đóng vai trò linking + mutual-exclusion resolver.

## Resolution

### `resolveEvent`

```solidity
function resolveEvent(uint256 eventId) external;
```

**Role**: Public (sau khi oracle có kết quả)

**Effects** (mutual exclusion):

1. Oracle trả về `winningIndex` (hoặc oracle-specific mechanism)
2. Child market tại `winningIndex` → `isResolved = true`, `outcome = true` (YES thắng)
3. Tất cả child khác → `isResolved = true`, `outcome = false` (NO thắng)
4. `resolvedAt = now`, `isResolved = true` cho EventGroup

**Event**: `EventResolved(eventId, winningIndex, winningChildMarketId)`

### `enableEventRefundMode`

```solidity
function enableEventRefundMode(uint256 eventId) external;
```

**Role**: `OPERATOR_ROLE`
**Use case**: Không xác định được winning index (tie, cancelled event).
**Effect**: `refundModeActive = true` cho EventGroup, mọi child chuyển sang refund mode.

**Event**: `EventRefundModeEnabled(eventId)`

## View functions

```solidity
function getEvent(uint256 eventId) external view returns (EventGroup memory);
function getEventMembers(uint256 eventId) external view returns (uint256[] memory);
function getMarketEventId(uint256 marketId) external view returns (uint256);  // 0 = standalone
```

## User flow ví dụ

**Setup**: Event "Ai thắng World Cup 2026?" với 4 child — Brazil, Argentina, Germany, France.

1. Admin `createEvent("World Cup 2026", ["Brazil wins?", "Argentina wins?", "Germany wins?", "France wins?"], endTime, oracle)` → 4 marketIds.
2. User `splitPosition(brazilMarketId, 10 USDC)` → nhận 10 YES-Brazil + 10 NO-Brazil.
3. User bán NO-Brazil giữ YES-Brazil → bet Brazil thắng.
4. Event kết thúc, Brazil thắng. Oracle report `winningIndex = 0`.
5. `resolveEvent(eventId)` → marketIds[0] (Brazil) outcome=true, 3 others outcome=false.
6. User `redeemMarketTokens(brazilMarketId)` → nhận USDC = YES holdings × $1 − fee.

## Cross-reference

- Child market vẫn tuân theo toàn bộ quy tắc của `MarketFacet`: split, merge, trade.
- Trading qua Router vẫn theo từng child market (orderbook + AMM pool riêng).
- Indexer track event group trong bảng `event_group`, child liên kết qua `market.eventId`. Xem [Indexer Schema](../indexer/01-schema.md).
- BE serve event qua `/api/v2/events` và `/api/v2/events/{id}`. Xem [Events Endpoints](../backend-api/08-endpoints-events.md).
