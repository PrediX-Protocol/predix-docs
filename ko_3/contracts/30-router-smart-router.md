---
description: Smart Router — Uniswap v4를 통한 CLOB + AMM 결합 실행
---

# Router (Smart Router)

PrediXRouter는 CLOB (Exchange)과 AMM (Uniswap v4)의 유동성을 통합해 트레이더에게 최적의 실행을 제공합니다.

## 거래 함수

```solidity
buyYes(bytes32 marketId, uint256 usdcIn, uint256 minYesOut,
       address recipient, uint256 deadline) → uint256 yesOut

sellYes(bytes32 marketId, uint256 yesIn, uint256 minUsdcOut,
        address recipient, uint256 deadline) → uint256 usdcOut

buyNo(bytes32 marketId, uint256 usdcIn, uint256 mintAmount,
      uint256 minNoOut, address recipient, uint256 deadline) → uint256 noOut

sellNo(bytes32 marketId, uint256 noIn, uint256 minUsdcOut,
       address recipient, uint256 deadline) → uint256 usdcOut
```

## Quote 함수

```solidity
quoteBuyYes(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellYes(bytes32 marketId, uint256 yesIn) → uint256
quoteBuyNo(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellNo(bytes32 marketId, uint256 noIn) → uint256
```

## 라우팅 로직

```
buyYes($100):
  1. CLOB에서 AMM보다 유리한 SELL_YES 주문 확인
  2. CLOB 매칭 (슬리피지 없음, 지정가)
  3. 나머지는 Uniswap v4 AMM swap으로 라우팅
  4. 전체 YES 수량을 recipient에게 전달
```

## Virtual NO Pricing

YES/USDC 풀만 있으므로:

* **NO 매수**: USDC split → YES+NO → AMM에서 YES 매도 → NO 보유
* **NO 매도**: AMM에서 YES 매수 → YES+NO merge → USDC 반환

## Flash Accounting

Router는 Uniswap v4의 Flash Accounting(unlock callback 패턴)을 사용해 가스 효율적으로 다단계 작업을 실행합니다.

> ⚠️ **파라미터**: 항상 `deadline`(보통 5분)과 `minOut`(슬리피지 보호)을 설정하세요. 조건 미충족 시 트랜잭션이 revert됩니다.
