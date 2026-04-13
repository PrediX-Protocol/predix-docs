---
description: YES 및 NO 토큰 - 완전한 DeFi 호환성을 갖춘 ERC-20 표준
---

# 결과 토큰

## 개요

각 PrediX 마켓은 두 개의 **ERC-20** 결과 토큰을 생성합니다:

| 토큰 | $1.00 지급 조건 | $0.00 지급 조건 |
| ---- | --------------- | --------------- |
| **YES** | 이벤트 발생 | 이벤트 미발생 |
| **NO** | 이벤트 미발생 | 이벤트 발생 |

## 기술 사양

| 속성 | 값 |
| ---- | -- |
| 표준 | ERC-20 + ERC-2612 (Permit) |
| 소수점 | 6 (USDC와 동일) |
| 발행 권한 | Diamond 컨트랙트만 (`onlyFactory` 수정자) |
| 소각 권한 | Diamond 컨트랙트만 |
| 불변 필드 | `factory`, `marketId`, `isYesToken` |

## ERC-2612 Permit

결과 토큰은 `permit` 함수를 통한 가스리스 승인을 지원합니다. 이를 통해 오프체인 서명을 사용하여 단일 트랜잭션에서 승인과 전송을 수행할 수 있습니다.

```typescript
// 오프체인에서 permit 서명
const deadline = Math.floor(Date.now() / 1000) + 3600;
const nonce = await yesToken.nonces(userAddress);
const signature = await signer.signTypedData(domain, types, { owner, spender, value, nonce, deadline });

// 온체인에서 permit 실행 (사전 approve 불필요)
await yesToken.permit(owner, spender, value, deadline, v, r, s);
```

## 핵심 불변 조건

```
YES.totalSupply == NO.totalSupply == market.totalCollateral
```

이 불변 조건은 **모든** 작업에서 유지됩니다: `splitPosition`, `mergePositions`, `groupSplit`, `groupMerge`, `redeemMarketTokens`, `refund`.

## 주소 생성

YES 토큰 주소는 USDC 주소보다 작아야 합니다 (Uniswap v4 풀 정렬 요구사항). PrediX는 **CREATE2 솔트 마이닝**을 사용하여 호환되는 주소를 결정론적으로 찾습니다.

> ⚠️ **중요**: 결과 토큰은 Diamond 컨트랙트에 의해서만 발행 및 소각될 수 있습니다. 공개 발행 함수는 없습니다.

---

**다음**: [분할 & 병합](split-and-merge.md) · [수수료](fees.md) · [거래 개요](../trading/overview.md)
