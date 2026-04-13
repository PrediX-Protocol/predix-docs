---
description: 99틱 비트맵과 3단계 매칭을 갖춘 온체인 CLOB
---

# Exchange 컨트랙트

PrediXExchange는 가스 효율적인 가격 스캔을 위한 99틱 비트맵을 갖춘 온체인 CLOB(Central Limit Order Book)를 구현합니다.

## 핵심 함수

### 주문 제출

```solidity
placeOrder(bytes32 marketId, uint8 side, uint256 price, uint256 amount)
    → (bytes32 orderId, uint256 filledAmount)
```

기존 주문과의 매칭을 시도합니다. 미체결 부분은 대기 주문이 됩니다.

### 주문 취소

```solidity
cancelOrder(bytes32 orderId)
```

소유자는 언제든지 취소할 수 있습니다. 마켓 만료 후에는 누구나 취소할 수 있습니다.

### 시장가 주문 체결

```solidity
fillMarketOrder(bytes32 marketId, uint8 side, uint256 maxPrice,
                uint256 amount, address taker, address recipient)
    → (uint256 filledAmount, uint256 totalCost)
```

하이브리드 실행의 CLOB 부분을 위해 Router가 사용합니다.

## 조회 함수

```solidity
getOrder(bytes32 orderId) → Order
getBestPrices(bytes32 marketId) → (bestBidYes, bestAskYes, bestBidNo, bestAskNo)
getDepthAtPrice(bytes32 marketId, uint8 side, uint256 price) → uint256
getOrderBook(bytes32 marketId, uint8 depth) → (yesBids, yesAsks, noBids, noAsks)
```

## 매칭 알고리즘

주문은 3단계로 매칭됩니다:

1. **COMPLEMENTARY**: 직접 상대방 (BUY_YES ↔ SELL_YES)
2. **MINT**: 두 매수 주문 (BUY_YES + BUY_NO, 합계 ≥ $1.00)
3. **MERGE**: 두 매도 주문 (SELL_YES + SELL_NO, 합계 ≤ $1.00)

## 제약 조건

| 파라미터 | 값 |
| --------- | ----- |
| 가격 범위 | `10000` – `990000` ($0.01 – $0.99, 6자리) |
| 가격 단위 | `10000` ($0.01) |
| 최소 주문 | `1000000` ($1.00) |
| 사용자당 마켓당 최대 주문 수 | 50 |
| 주문당 최대 체결 수 | 20 |

## 일시정지 메커니즘

```solidity
pause()            // PAUSE_ADMIN 전용
unpause()          // PAUSE_ADMIN 전용
setPauseAdmin(address) // 현재 PAUSE_ADMIN 전용
```

---

**다음**: [Router](router.md) · [매칭 엔진](../trading/matching-engine.md) · [지정가 주문](../trading/limit-orders.md)
