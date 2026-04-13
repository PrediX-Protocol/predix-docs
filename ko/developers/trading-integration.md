---
description: 애플리케이션에 PrediX 거래 연동하기
---

# 거래 연동

## 1단계: 토큰 승인

```typescript
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

// 시장가 주문을 위해 Router 승인
await usdc.approve(ROUTER_ADDRESS, ethers.MaxUint256);

// 지정가 주문을 위해 Exchange 승인
await usdc.approve(EXCHANGE_ADDRESS, ethers.MaxUint256);

// 분할/병합을 위해 Diamond 승인
await usdc.approve(DIAMOND_ADDRESS, ethers.MaxUint256);
```

## 2단계: 시장가 주문 (Router 경유)

```typescript
const router = new ethers.Contract(ROUTER_ADDRESS, ROUTER_ABI, signer);
const marketId = "0x...";
const deadline = Math.floor(Date.now() / 1000) + 300;

// 먼저 견적 조회
const usdcIn = ethers.parseUnits("100", 6);
const expectedYes = await router.quoteBuyYes(marketId, usdcIn);
const minYesOut = expectedYes * 98n / 100n; // 2% 슬리피지

// 실행
const tx = await router.buyYes(marketId, usdcIn, minYesOut, signer.address, deadline);
await tx.wait();
```

## 3단계: 지정가 주문 (Exchange 경유)

```typescript
const exchange = new ethers.Contract(EXCHANGE_ADDRESS, EXCHANGE_ABI, signer);

// $0.65에 YES 매수
const side = 0; // BUY_YES
const price = ethers.parseUnits("0.65", 6);
const amount = ethers.parseUnits("100", 6);

const tx = await exchange.placeOrder(marketId, side, price, amount);
const receipt = await tx.wait();
```

## 4단계: 주문 취소

```typescript
const orderId = "0x..."; // OrderPlaced 이벤트에서 가져오기
await exchange.cancelOrder(orderId);
```

## 오류 처리

```typescript
try {
  await router.buyYes(marketId, usdcIn, minYesOut, signer.address, deadline);
} catch (error: any) {
  if (error.message.includes("Market_NotFound")) {
    console.error("유효하지 않은 마켓 ID");
  } else if (error.message.includes("Market_Ended")) {
    console.error("마켓이 만료되었습니다");
  } else if (error.message.includes("Market_ExceedsPerTradeCap")) {
    console.error("거래당 한도를 초과했습니다");
  } else if (error.message.includes("SlippageExceeded")) {
    console.error("가격이 너무 많이 변동했습니다. 슬리피지를 높이세요");
  }
}
```

---

**다음**: [분할 & 병합](split-merge.md) · [정산 & 상환](resolve-redeem.md) · [이벤트](events.md)
