---
description: 두 가지 거래소 - CLOB과 AMM - Smart Router로 통합
---

# 거래 개요

PrediX는 Smart Router로 통합된 두 가지 거래소를 제공합니다:

```
User → PrediXRouter (Smart Router)
          ├── PrediXExchange (CLOB) — 지정가 주문, 슬리피지 없음
          └── Uniswap v4 AMM — 항시 유동성 제공
```

## 시장가 주문 vs 지정가 주문

| 유형 | 경유 | 적합한 경우 |
| ---- | ---- | ----------- |
| **시장가 주문** | Router (`buyYes`, `sellYes`, `buyNo`, `sellNo`) | 즉시 체결, 최적 가격 |
| **지정가 주문** | Exchange (`placeOrder`) | 특정 가격, 인내심 있는 트레이더 |

## 거래 흐름

1. USDC(또는 토큰)를 컨트랙트에 **승인**
2. `quoteBuyYes` / `quoteSellYes` 등을 사용하여 예상 출력량 **조회**
3. 슬리피지 보호(`minOut` 파라미터)와 함께 거래 **실행**
4. 결과 토큰(또는 USDC) **수령**

> ⚠️ **항상 마감 시한을 설정하세요** (일반적으로 현재로부터 5분) 그리고 슬리피지 보호를 위한 **minOut**을 설정하세요.

---

**다음**: [시장가 주문](market-orders.md) · [지정가 주문](limit-orders.md) · [스마트 라우팅](smart-routing.md)
