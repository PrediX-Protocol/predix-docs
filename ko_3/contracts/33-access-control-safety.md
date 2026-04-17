---
description: 역할 기반 접근 제어 — 세분화된 권한의 4가지 역할
---

# Access Control & Safety

## 역할

| 역할            | ID | 목적                        |
| ------------- | -- | ------------------------- |
| DEFAULT\_ADMIN | 0  | 모든 역할 관리                  |
| ADMIN         | 1  | 마켓 생성, 수수료 설정, 오라클 승인     |
| OPERATOR      | 2  | 긴급 결과 확정, 환불 모드 활성화       |
| PAUSER        | 3  | 시스템 일시 정지                 |

## 권한 매트릭스

| 함수                    | PUBLIC | ADMIN | OPERATOR   | PAUSER | OWNER |
| --------------------- | ------ | ----- | ---------- | ------ | ----- |
| `createMarket`        |        | ✓     |            |        |       |
| `splitPosition`       | ✓      |       |            |        |       |
| `mergePositions`      | ✓      |       |            |        |       |
| `resolveMarket`       | ✓      |       |            |        |       |
| `redeemMarketTokens`  | ✓      |       |            |        |       |
| `placeOrder`          | ✓      |       |            |        |       |
| `buyYes` (Router)     | ✓      |       |            |        |       |
| `emergencyResolve`    |        |       | ✓ (7일)     |        |       |
| `enableRefundMode`    |        |       | ✓          |        |       |
| `setTvlCap`           |        | ✓     |            |        |       |
| `setApprovedOracle`   |        | ✓     |            |        |       |
| `pauseAll`            |        |       |            | ✓      |       |
| `diamondCut`          |        |       |            |        | ✓     |

## 2단계 소유권 이전

```
1. 현재 소유자가 transferOwnership(newOwner) 호출
2. newOwner가 pendingOwner가 됨
3. newOwner가 acceptOwnership() 호출
4. 소유권 이전 완료
```

잘못된 주소로 실수로 이전되는 것을 방지합니다.

## 마지막 관리자 보호

시스템은 마지막 관리자의 역할 해제를 방지해 프로토콜에 항상 최소 1명의 관리자가 있도록 보장합니다.
