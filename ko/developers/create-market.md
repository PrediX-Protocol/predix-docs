---
description: 프로그래밍 방식으로 예측 마켓 생성
---

# 마켓 생성

> ⚠️ 마켓 생성에는 **ADMIN_ROLE**이 필요합니다.

## 단계별 가이드

```typescript
import { ethers } from "ethers";

const diamond = new ethers.Contract(DIAMOND_ADDRESS, DIAMOND_ABI, signer);

// 매개변수
const question = "Will BTC exceed $100,000 by December 31, 2026?";
const description = "Resolves YES if Bitcoin price (Chainlink BTC/USD) >= $100,000.";
const endTime = Math.floor(new Date("2026-12-31T23:59:59Z").getTime() / 1000);
const categoryId = ethers.ZeroHash; // 카테고리 없음 (독립형 마켓)
const oracle = "0x699A8C74663b1C852E195b2ffa00D5965E992Cf3"; // ManualOracle

// 생성
const tx = await diamond.createMarket(question, description, endTime, categoryId, oracle);
const receipt = await tx.wait();

// MarketCreated 이벤트 파싱
const event = receipt.logs.find(
  (log) => diamond.interface.parseLog(log)?.name === "MarketCreated"
);
const parsed = diamond.interface.parseLog(event);
const marketId = parsed.args.marketId;

console.log("마켓 ID:", marketId);
console.log("YES 토큰:", parsed.args.yesToken);
console.log("NO 토큰:", parsed.args.noToken);
```

## 자동으로 수행되는 작업

`createMarket`이 호출되면:

1. YES 및 NO ERC-20 토큰이 배포됩니다 (CREATE2)
2. Uniswap v4 AMM 풀 (YES/USDC)이 생성되고 $0.50으로 초기화됩니다
3. 풀이 Hook에 등록됩니다
4. 마켓 데이터가 Diamond 스토리지에 저장됩니다

## 매개변수

| 매개변수 | 타입 | 설명 |
| --------- | ---- | ----------- |
| `question` | string | 마켓 질문 |
| `description` | string | 추가 설명 |
| `endTime` | uint256 | 거래가 중단되는 Unix 타임스탬프 |
| `categoryId` | bytes32 | 카테고리 ID (독립형은 `bytes32(0)`) |
| `oracle` | address | 승인된 오라클 어댑터 주소 |

## 온체인 검증

```typescript
const market = await diamond.getMarket(marketId);
console.log("존재 여부:", await diamond.isMarketExists(marketId));
console.log("오라클:", market.oracle);
console.log("종료 시간:", new Date(Number(market.endTime) * 1000));
```

---

**다음**: [거래 연동](trading-integration.md) · [분할 & 병합](split-merge.md)
