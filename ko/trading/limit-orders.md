---
description: 온체인 CLOB에서 지정가 주문 배치 및 관리
---

# 지정가 주문

지정가 주문은 PrediXExchange (CLOB)에 직접 배치됩니다. 체결, 취소 또는 마켓 만료까지 온체인에 유지됩니다.

## 주문 배치

```typescript
const exchange = new ethers.Contract(
  "0xa202abCb2A358c0862B2dA76b553398339F2C638",
  EXCHANGE_ABI,
  signer
);

// Side 열거형: 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
const side = 0; // BUY_YES
const price = ethers.parseUnits("0.65", 6);   // 토큰당 $0.65
const amount = ethers.parseUnits("100", 6);    // 100 토큰

// 매수 주문의 경우: USDC를 Exchange에 승인
// deposit = price × amount / 1e6
await usdc.approve(exchange.target, price * amount / 1000000n);

const tx = await exchange.placeOrder(marketId, side, price, amount);
const receipt = await tx.wait();
// OrderPlaced 이벤트에서 orderId 파싱
```

## 주문 취소

```typescript
await exchange.cancelOrder(orderId);
// 미체결 예치금이 주문 소유자에게 반환
```

> ⚠️ 만료된 마켓 주문은 누구나 취소할 수 있습니다 (endTime 이후).

## 제약 조건

| 파라미터 | 값 |
| -------- | -- |
| 가격 범위 | $0.01 ~ $0.99 (6 소수점으로 `10000` ~ `990000`) |
| 가격 단위 | $0.01 (`10000`) |
| 최소 주문 수량 | $1.00 (6 소수점으로 `1000000`) |
| 사용자당 마켓당 최대 주문 수 | 50 |
| 배치당 최대 체결 수 | 20 |
| 자기 거래 방지 | maker.owner ≠ taker.owner |

## Side 열거형

```solidity
enum Side {
    BUY_YES,   // 0
    SELL_YES,  // 1
    BUY_NO,    // 2
    SELL_NO    // 3
}
```

---

**다음**: [매칭 엔진](matching-engine.md) · [스마트 라우팅](smart-routing.md) · [오더북](order-book.md)
