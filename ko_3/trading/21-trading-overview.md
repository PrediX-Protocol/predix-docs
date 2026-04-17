# 21. Trading Overview

### Prediction 토큰을 사고파는 두 가지 방법

***

#### 📋 오더북 (CLOB)

Binance 같은 전통 거래소처럼 작동합니다. 특정 가격으로 매수/매도 주문을 내고 **누군가 매칭해 주길 기다립니다**.

* 매수/매도 가격을 정확히 컨트롤 가능
* 급하지 않고 최고의 가격을 원할 때 적합

***

#### 💧 자동 유동성 (AMM)

환전소처럼 작동 — 항상 가격이 제공되고 **즉시 거래**되며, 매칭을 기다릴 필요가 없습니다.

* 언제든지 즉시 매수/매도 가능
* 시장 유동성이 낮으면 오더북보다 가격이 약간 불리할 수 있음

***

### 선택할 필요 없음 — 시스템이 대신 처리

모든 거래는 **Smart Router**를 통과합니다 — 양쪽 가격을 자동으로 비교하고 **가장 유리한 경로를 선택**하는 지능형 라우터입니다.

```
주문 → Router가 양쪽 가격 확인
     → 한 쪽에서 일부, 다른 쪽에서 일부 매수 가능
     → 항상 가능한 최고의 가격을 받음
```

***

### 거래 시 두 가지 주문 유형

<table data-header-hidden><thead><tr><th width="211"></th><th></th><th></th></tr></thead><tbody><tr><td></td><td><strong>Market Order</strong></td><td><strong>Limit Order</strong></td></tr><tr><td><strong>작동 방식</strong></td><td>즉시 매수/매도</td><td>원하는 가격 지정, 매칭 대기</td></tr><tr><td><strong>속도</strong></td><td>즉시</td><td>수 분–수 시간 걸릴 수 있음</td></tr><tr><td><strong>가격 컨트롤</strong></td><td>없음</td><td>있음 — 가격을 직접 결정</td></tr><tr><td><strong>적합한 때</strong></td><td>빠르게 진입하고 싶을 때</td><td>더 나은 가격, 급하지 않을 때</td></tr></tbody></table>

> 💡 **Tip:** 처음이라면 **Market Order**를 사용하세요 — 빠르고 간단하며, Router가 나머지를 처리합니다. 익숙해지면 **Limit Order**로 매수/매도 가격을 최적화하세요.
