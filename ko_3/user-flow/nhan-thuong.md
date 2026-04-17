---
description: Market resolve 이후 보상 수령 방법
---

# 보상 수령

## Market Resolve 시

Market이 종료되고 oracle이 결과를 확정한 후:

```
Market: "BTC $85K 도달?" → 결과: YES ✅

YES shares → share당 $1.00 가치
NO shares → $0.00
```

## 보상 수령 방법

1. Resolve된 market으로 이동
2. **"Redeem"** 버튼이 표시됨
3. **"Redeem"** 클릭
4. 지갑에서 확인
5. USDC가 즉시 지갑으로 전송

```
보유: 65 YES shares
결과: YES 승리
수령: 65 × $1.00 = $65 USDC → 지갑으로
```

### 예측이 틀리면?

**패배** 쪽 shares를 보유한 경우:

```
보유: 50 YES shares
결과: NO 승리
YES shares = $0.00
수령할 것 없음. 할 일 없음.
```

> 추가로 잃는 것 없음 — 처음 share를 위해 지불한 금액만 손실.

## YES와 NO 둘 다 보유한 경우?

```
보유: 30 YES + 20 NO
결과: YES 승리

YES 수령: 30 × $1.00 = $30 USDC ✅
NO shares: 가치 없음 ($0) ❌
총 수령: $30
```

## 보상 수령을 놓칠 수 있나요?

수령에 **마감 시한 없음**. Resolve 이후 언제든 수령 가능. 보상은 만료되지 않습니다.

## 다중 결과 market

"누가 선거에서 이기나?" 같은 market의 경우:

```
보유:
  Trump YES: 50 shares
  Harris YES: 30 shares

승자: Trump (index 0)

수령:
  Trump YES: 50 × $1.00 = $50 ✅
  Harris YES: 가치 없음 ❌

추가: Harris NO shares가 승자!
  (Harris가 지지 않았기 때문)
```
