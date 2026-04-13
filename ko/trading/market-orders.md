---
description: Smart Router를 통한 시장가 주문 실행
---

# 시장가 주문

시장가 주문은 Router를 통해 즉시 실행되며, CLOB과 AMM의 유동성을 모두 집계합니다.

## 함수

| 함수 | 동작 |
| ---- | ---- |
| `buyYes(marketId, usdcIn, minYesOut, recipient, deadline)` | USDC 지불, YES 토큰 수령 |
| `sellYes(marketId, yesIn, minUsdcOut, recipient, deadline)` | YES 토큰 지불, USDC 수령 |
| `buyNo(marketId, usdcIn, mintAmount, minNoOut, recipient, deadline)` | USDC 지불, NO 토큰 수령 |
| `sellNo(marketId, noIn, minUsdcOut, recipient, deadline)` | NO 토큰 지불, USDC 수령 |

## 코드 예시

### YES 매수

```typescript
import { ethers } from "ethers";

const router = new ethers.Contract(
  "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7",
  ROUTER_ABI,
  signer
);
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

const marketId = "0x...";
const usdcIn = ethers.parseUnits("100", 6);    // 100 USDC
const minYesOut = ethers.parseUnits("90", 6);   // 2% 슬리피지
const deadline = Math.floor(Date.now() / 1000) + 300; // 5분

// 1단계: USDC를 Router에 승인
await usdc.approve(router.target, usdcIn);

// 2단계: YES 매수
const tx = await router.buyYes(marketId, usdcIn, minYesOut, signer.address, deadline);
const receipt = await tx.wait();
console.log("거래 체결:", receipt.hash);
```

### YES 매도

```typescript
const yesToken = new ethers.Contract(market.yesToken, ERC20_ABI, signer);
const yesIn = ethers.parseUnits("100", 6);
const minUsdcOut = ethers.parseUnits("60", 6);

// YES 토큰을 Router에 승인
await yesToken.approve(router.target, yesIn);

// YES 매도
await router.sellYes(marketId, yesIn, minUsdcOut, signer.address, deadline);
```

### NO 매수

```typescript
// NO 매수에는 mintAmount 파라미터가 필요 (내부적으로 분할에 사용)
const usdcIn = ethers.parseUnits("100", 6);
const mintAmount = ethers.parseUnits("100", 6); // 분할할 수량
const minNoOut = ethers.parseUnits("90", 6);

await usdc.approve(router.target, usdcIn);
await router.buyNo(marketId, usdcIn, mintAmount, minNoOut, signer.address, deadline);
```

## 거래 전 시세 조회

항상 실행 전에 시세를 조회하세요:

```typescript
const expectedYes = await router.quoteBuyYes(marketId, usdcIn);
console.log("예상 YES:", ethers.formatUnits(expectedYes, 6));

// 슬리피지 허용 범위를 설정한 minOut
const minYesOut = expectedYes * 98n / 100n; // 2% 슬리피지
```

---

**다음**: [지정가 주문](limit-orders.md) · [스마트 라우팅](smart-routing.md) · [개발자 거래 통합](../developers/trading-integration.md)
