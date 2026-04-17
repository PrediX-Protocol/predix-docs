---
description: >-
  Safety cap, 긴급 제어, 재진입 방어 및 샌드위치 공격 방어
---

# 보안

## Safety Caps

| 제한               | 설명                         | 설정 권한 |
| ---------------- | -------------------------- | ----- |
| TVL Cap          | 시스템 내 최대 USDC              | ADMIN |
| Per-Trade Cap    | 거래/split당 최대 USDC          | ADMIN |
| Per-Market Cap   | 마켓당 최대 USDC               | ADMIN |

제한은 보수적으로 시작해 프로토콜 성숙도에 따라 상향됩니다.

## 긴급 제어

| 메커니즘           | 역할       | 설명                                     |
| -------------- | -------- | -------------------------------------- |
| 모듈 Pause       | PAUSER   | 특정 market 모듈 동결                        |
| 전체 Pause       | PAUSER   | 전체 시스템 동결                              |
| 긴급 결과 확정       | OPERATOR | 7일 지연 후 결과 확정 (oracle 실패)            |
| Refund Mode    | OPERATOR | 모든 홀더에게 비율대로 환불                        |

## 재진입 방어

세 개의 독립된 가드가 서로 다른 컨트랙트 경계를 보호:

1. **Diamond 스토리지 가드**: MarketFacet 함수 보호
2. **Exchange/Router transient 스토리지**: CLOB 및 라우팅 보호
3. **Hook 상태 가드**: Uniswap v4 Hook callback 보호

20개 함수가 `nonReentrant` modifier로 보호됩니다.

## 샌드위치 방어

프로토콜 레벨에서 Hook에 내장. 같은 사용자의 동일 블록 내 반대 방향 swap을 감지.

추가 레이어: Unichain이 시퀀서 레벨의 revert 보호 제공.

## 불변식

```
1. YES.totalSupply == NO.totalSupply == totalCollateral (항상)
2. Split(N) + Merge(N) = 항등 (가치 누수 없음)
3. 총 redeem ≤ 총 예치
4. Exchange 잔고 ≥ 전체 대기 주문 예치 합계
5. Refund Mode는 모든 거래(split, merge, CLOB, AMM)를 차단
```
