---
description: 스마트 Router — Uniswap v4를 통한 하이브리드 CLOB + AMM 실행
---

# Router 컨트랙트

PrediXRouter는 CLOB (Exchange)와 AMM (Uniswap v4)의 유동성을 통합하여 트레이더에게 최적의 실행을 제공합니다.

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

## 견적 함수

```solidity
quoteBuyYes(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellYes(bytes32 marketId, uint256 yesIn) → uint256
quoteBuyNo(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellNo(bytes32 marketId, uint256 noIn) → uint256
```

## 라우팅 로직

```
buyYes($100):
  1. AMM 가격보다 유리한 CLOB의 SELL_YES 주문 확인
  2. CLOB 체결 (슬리피지 없음, 지정가)
  3. 나머지를 Uniswap v4 AMM 스왑으로 라우팅
  4. 총 YES를 수신자에게 반환
```

## 가상 NO 가격 책정

YES/USDC 풀만 존재하므로:

- **NO 매수**: USDC를 YES+NO로 분할 → AMM을 통해 YES 매도 → NO 보유
- **NO 매도**: AMM에서 YES 매수 → YES+NO 병합 → USDC 반환

## Flash Accounting

Router는 Uniswap v4의 Flash Accounting (unlock 콜백 패턴)을 사용하여 가스 효율적인 다단계 작업을 수행합니다.

> ⚠️ **파라미터**: 항상 `deadline` (일반적으로 5분)과 `minOut` (슬리피지 보호)을 설정하세요. 조건이 충족되지 않으면 트랜잭션이 되돌려집니다.

---

**다음**: [Hook](hook.md) · [스마트 라우팅](../trading/smart-routing.md) · [시장가 주문](../trading/market-orders.md)
