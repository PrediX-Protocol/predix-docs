---
description: YES/NO share 매수와 매도
---

# Share (YES / NO) 매매

## Share 매수 (Market Order)

Market order는 현재 최고 가격에 즉시 매수합니다.

### YES 매수

1. Market 선택 → **YES** 클릭
2. 주문 유형 **"Market"** 선택
3. USDC 금액 입력 (예: $50)
4. 예상치 확인: "~130 YES shares"
5. **"Buy YES — $0.38"** 클릭
6. 지갑에서 확인 → 완료!

### NO 매수

절차 동일하지만 **NO** 클릭:

1. **NO** 클릭
2. 금액 입력
3. **"Buy NO — $0.62"** 클릭

## Share 매도

### YES 매도

1. **"Sell"** 탭으로 전환
2. **YES** 클릭
3. Share 수 입력 (또는 **MAX** 클릭)
4. 예상치 확인: "~$48 USDC"
5. **"Sell YES"** 클릭

### NO 매도

유사하지만 **NO** 선택.

## Limit Order

특정 가격을 원한다면? **Limit order** 사용.

### 매수 Limit 주문

1. 주문 유형 **"Limit"**으로 전환
2. 가격 설정 (예: $0.30)
3. 수량 설정 (예: 100 shares)
4. **"Place Order"** 클릭
5. 누군가 $0.30에 매도할 때까지 주문 대기

```
비용: 100 × $0.30 = $30 USDC (체결 또는 취소 전까지 락업)
```

### 매도 Limit 주문

1. **"Sell"** 탭 → **"Limit"**
2. 가격 설정 (예: $0.70)
3. 수량 설정
4. **"Place Order"** 클릭

### Limit Order 취소

* 주문 목록에서 열린 주문 찾기
* **"Cancel"** 클릭
* 락업된 USDC 또는 토큰이 즉시 반환

## Limit Order 가격 규칙

```
최소 가격: $0.01
최대 가격: $0.99
가격 단위: $0.01 ($0.35 가능, $0.355 불가)
최소 주문: $1.00
최대 열린 주문: market당 50
```

## Market vs Limit Order

|        | Market Order   | Limit Order         |
| ------ | -------------- | ------------------- |
| 속도     | 즉시             | 원하는 가격 대기           |
| 가격     | 현재 최고가         | 정확히 설정한 가격          |
| 적합한 때  | 빠른 진입/청산       | 더 나은 가격 확보          |
| 리스크    | 슬리피지 가능        | 미체결 가능              |

> 💡 **Tip**: 속도가 필요할 때 market order, 가격 컨트롤이 필요할 때 limit order.
