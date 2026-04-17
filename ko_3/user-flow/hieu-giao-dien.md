---
description: PrediX 거래 인터페이스 이해
---

# 인터페이스 이해

### 주요 레이아웃 <a href="#bo-cuc-chinh" id="bo-cuc-chinh"></a>

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

설명: _Price Chart_ = 가격 차트, _Your Position_ = 내 포지션, _Trading Panel_ = 거래 패널, _Order Book_ = 오더북, _Split / Merge_ = 분할 / 결합, _Market Info_ = 시장 정보.

### Your Position — 내 포지션 (우측 상단) <a href="#your-position-vi-the-cua-ban-tren-phai" id="your-position-vi-the-cua-ban-tren-phai"></a>

이 market에서 보유 중인 것을 표시:

```
Your Position
  YES    65 shares     ← YES 토큰 65개 보유
  NO     0 shares      ← NO 토큰 미보유
```

## Trading Panel — 거래 패널 (우측)

#### YES / NO 버튼 <a href="#nut-yes-no" id="nut-yes-no"></a>

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

* 녹색 강조 = 선택됨
* 표시 가격 = 현재 시장 가격

#### Buy / Sell 전환 <a href="#chuyen-doi-buy-sell" id="chuyen-doi-buy-sell"></a>

* **Buy**: USDC로 share 구매
* **Sell**: Share를 매도해 USDC 수령

#### Market / Limit 전환 <a href="#chuyen-doi-market-limit" id="chuyen-doi-market-limit"></a>

* **Market**: 현재 가격에 즉시 매수/매도
* **Limit**: 지정 가격, 매칭 대기

#### 금액 입력 (Amount) <a href="#nhap-so-tien-amount" id="nhap-so-tien-amount"></a>

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

* 금액 입력 또는 프리셋 버튼 클릭
* **MAX** = 전체 잔액
* **Balance** = 현재 잔액 (참고용)

#### 실행 버튼 <a href="#nut-thuc-hien" id="nut-thuc-hien"></a>

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

동작과 가격 표시.

## Price Chart — 가격 차트 (좌측)

시간에 따른 가격 이력 표시:

* 녹색 선 = 가격 상승
* 빨간 선 = 가격 하락
* 마우스 호버로 특정 시점 가격 확인

## Order Book — 오더북 (좌측 하단)

```
Price    Qty        Total
$0.40    61.58      543.73    ← Asks (매도자)
$0.39    69.68      482.15
$0.38    70.34      412.47

         $0.38   Spread: $0.03      ← Mid price

$0.35    24.67      24.67     ← Bids (매수자)
$0.34    31.87      56.54
$0.33    31.29      87.83
```

* **빨간색 (위)** = Asks — 이 가격에 매도하려는 사람
* **녹색 (아래)** = Bids — 이 가격에 매수하려는 사람
* **Spread** = 최고 bid와 최고 ask 사이의 간격

## Split / Merge — 분할 / 결합

```
┌──────────────────────────────┐
│  [Split]        [Merge]      │
│                              │
│  USDC를 동일한 수의             │
│  YES + NO 토큰으로 분할        │
│                              │
│  ┌────────────────────┐      │
│  │ 100.00        USDC │      │
│  └────────────────────┘      │
│                              │
│  ┌────────────────────┐      │
│  │       Split         │      │
│  └────────────────────┘      │
└──────────────────────────────┘
```

* **Split (분할)**: $100 USDC → 100 YES + 100 NO 토큰
* **Merge (결합)**: 100 YES + 100 NO → $100 USDC
* 고급 전략에 유용

## Market Info — 시장 정보

```
Market Info
  Category:          crypto
  Total Collateral:  $9,936.67 USDC
  Status:            active
  Oracle:            0x699a8c...992cf3
```

* **Total Collateral**: market에 락업된 총 USDC
* **Status**: active / ended / resolved
* **Oracle**: 결과를 확정하는 자

***
