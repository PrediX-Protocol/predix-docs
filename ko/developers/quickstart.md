---
description: 5분 만에 PrediX 개발 시작하기
---

# 개발자 빠른 시작

## 사전 요구 사항

- Node.js 18+
- ethers.js v6

## 설정

```bash
npm install ethers
```

## Unichain Sepolia 연결

```typescript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider("https://sepolia.unichain.org");
const signer = new ethers.Wallet(process.env.PRIVATE_KEY!, provider);

// 컨트랙트 인스턴스
const DIAMOND = "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A";
const EXCHANGE = "0xa202abCb2A358c0862B2dA76b553398339F2C638";
const ROUTER = "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

const diamond = new ethers.Contract(DIAMOND, DIAMOND_ABI, signer);
const router = new ethers.Contract(ROUTER, ROUTER_ABI, signer);
const usdc = new ethers.Contract(USDC, ERC20_ABI, signer);
```

## 마켓 조회

```typescript
const marketId = "0x..."; // MarketCreated 이벤트 또는 API에서 가져오기
const market = await diamond.getMarket(marketId);

console.log("질문:", market.question);
console.log("종료 시간:", new Date(Number(market.endTime) * 1000));
console.log("YES 토큰:", market.yesToken);
console.log("NO 토큰:", market.noToken);
console.log("정산 완료:", market.isResolved);
console.log("담보금:", ethers.formatUnits(market.totalCollateral, 6), "USDC");
```

## 첫 번째 거래 실행

```typescript
const usdcAmount = ethers.parseUnits("10", 6); // 10 USDC
const deadline = Math.floor(Date.now() / 1000) + 300; // 5분

// 1단계: 견적 조회
const expectedYes = await router.quoteBuyYes(marketId, usdcAmount);
const minYesOut = expectedYes * 95n / 100n; // 5% 슬리피지

// 2단계: USDC 승인
await (await usdc.approve(ROUTER, usdcAmount)).wait();

// 3단계: YES 토큰 구매
const tx = await router.buyYes(marketId, usdcAmount, minYesOut, signer.address, deadline);
const receipt = await tx.wait();

console.log("거래 해시:", receipt.hash);
```

---

**다음**: [컨트랙트 주소](addresses.md) · [ABI](abis.md) · [거래 연동](trading-integration.md)
