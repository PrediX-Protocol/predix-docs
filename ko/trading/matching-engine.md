---
description: 3단계 매칭 알고리즘 - COMPLEMENTARY, MINT, MERGE
---

# 매칭 엔진

PrediXExchange는 세 가지 매칭 유형에 걸쳐 가스 효율적인 주문 스캔을 위해 99-틱 비트맵을 사용합니다.

## 매칭 유형

### COMPLEMENTARY - 직접 스왑

동일 토큰에 대한 두 개의 반대 주문:

```
BUY_YES @ $0.60  ↔  SELL_YES @ $0.50
→ 메이커 가격($0.50)으로 매칭
→ 매수자는 YES 수령, 매도자는 USDC 수령
→ 매수자는 $0.10 가격 개선 (환불)
```

### MINT - 양쪽 모두 매수 (합산 >= $1.00)

```
BUY_YES @ $0.60  +  BUY_NO @ $0.50  =  $1.10 ≥ $1.00
→ USDC 분할 → YES + NO
→ YES는 YES 매수자에게, NO는 NO 매수자에게 전달
→ 잉여금 $0.10 → 프로토콜 수수료
```

### MERGE - 양쪽 모두 매도 (합산 <= $1.00)

```
SELL_YES @ $0.40  +  SELL_NO @ $0.50  =  $0.90 ≤ $1.00
→ YES + NO 병합 → USDC
→ USDC를 가격에 따라 매도자 간 분배
→ 잉여금 $0.10 → 프로토콜 수수료
```

## MatchType 열거형

```solidity
enum MatchType {
    COMPLEMENTARY, // 0
    MINT,          // 1
    MERGE          // 2
}
```

## 매칭 우선순위

새 주문이 배치되면 엔진은 다음 순서로 매칭을 시도합니다:

1. **COMPLEMENTARY** - 직접 상대방 주문 확인
2. **MINT** - 반대쪽 매수 주문과 쌍을 이룰 수 있는지 확인
3. **MERGE** - 반대쪽 매도 주문과 쌍을 이룰 수 있는지 확인
4. **대기** - 미체결 수량은 오더북에 대기 주문으로 등록

---

**다음**: [오더북](order-book.md) · [가상 NO 가격 책정](virtual-no-pricing.md)
