---
description: 99-tick 비트맵과 3단계 매칭을 가진 온체인 CLOB
---

# Exchange (CLOB)

## On-Chain Order Book

PrediXExchange는 가스 효율적인 가격 스캔을 위해 99-tick 비트맵 ($0.01–$0.99)을 갖춘 온체인 Central Limit Order Book(CLOB)을 구현합니다. 각 tick = 10000 단위(6 decimals). 주문은 온체인에 저장.

## 핵심 함수

### 주문 등록

```solidity
placeOrder(bytes32 marketId, uint8 side, uint256 price, uint256 amount)
    → (bytes32 orderId, uint256 filledAmount)
```

기존 주문과 매칭 시도. 매칭되지 않은 부분은 대기 주문이 됨.

### 주문 취소

```solidity
cancelOrder(bytes32 orderId)
```

주문자는 언제든 취소 가능. 마켓 만료 후에는 누구나 취소 가능.

### Market Order 실행

```solidity
fillMarketOrder(bytes32 marketId, uint8 side, uint256 maxPrice,
                uint256 amount, address taker, address recipient)
    → (uint256 filledAmount, uint256 totalCost)
```

Router가 결합 실행의 CLOB 부분에 사용.

## 조회 함수

```solidity
getOrder(bytes32 orderId) → Order
getBestPrices(bytes32 marketId) → (bestBidYes, bestAskYes, bestBidNo, bestAskNo)
getDepthAtPrice(bytes32 marketId, uint8 side, uint256 price) → uint256
getOrderBook(bytes32 marketId, uint8 depth) → (yesBids, yesAsks, noBids, noAsks)
```

## 매칭 알고리즘

주문은 3단계를 통해 매칭됩니다:

1. **COMPLEMENTARY**: 직접 상대 매칭 (BUY\_YES ↔ SELL\_YES)
2. **MINT**: 두 매수 주문 (BUY\_YES + BUY\_NO, 합 ≥ $1.00)
3. **MERGE**: 두 매도 주문 (SELL\_YES + SELL\_NO, 합 ≤ $1.00)

## 제약

<table><thead><tr><th width="307">파라미터</th><th>값</th></tr></thead><tbody><tr><td>가격 범위</td><td><code>10000</code> – <code>990000</code> ($0.01 – $0.99, 6 decimals)</td></tr><tr><td>가격 단위</td><td><code>10000</code> ($0.01)</td></tr><tr><td>최소 주문</td><td><code>1000000</code> ($1.00)</td></tr><tr><td>사용자/마켓당 최대 주문 수</td><td>50</td></tr><tr><td>주문당 최대 매칭 수</td><td>20</td></tr></tbody></table>

## Pause 메커니즘

```solidity
pause()            // PAUSE_ADMIN 전용
unpause()          // PAUSE_ADMIN 전용
setPauseAdmin(address) // 현재 PAUSE_ADMIN 전용
```
