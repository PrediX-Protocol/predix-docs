---
description: USDC를 토큰으로 분할하고 토큰을 다시 병합
---

# 분할 & 병합 (개발자 가이드)

## 포지션 분할

```typescript
const diamond = new ethers.Contract(DIAMOND_ADDRESS, DIAMOND_ABI, signer);
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

const marketId = "0x...";
const amount = ethers.parseUnits("100", 6); // 100 USDC

// Diamond에 USDC 승인
await (await usdc.approve(DIAMOND_ADDRESS, amount)).wait();

// 분할: 100 USDC → 100 YES + 100 NO
const tx = await diamond.splitPosition(marketId, amount);
await tx.wait();

// 잔액 확인
const market = await diamond.getMarket(marketId);
const yesToken = new ethers.Contract(market.yesToken, ERC20_ABI, provider);
const noToken = new ethers.Contract(market.noToken, ERC20_ABI, provider);

console.log("YES 잔액:", ethers.formatUnits(await yesToken.balanceOf(signer.address), 6));
console.log("NO 잔액:", ethers.formatUnits(await noToken.balanceOf(signer.address), 6));
```

## 포지션 병합

```typescript
const mergeAmount = ethers.parseUnits("50", 6); // 50 YES + 50 NO 병합

// 승인 불필요 (Diamond이 이미 소각 권한을 보유)
const tx = await diamond.mergePositions(marketId, mergeAmount);
await tx.wait();

// 결과: 50 USDC가 지갑으로 반환됩니다
```

> ⚠️ 병합하려면 **동일한** 양의 YES 및 NO 토큰을 보유해야 합니다. 병합 금액은 어느 토큰의 잔액도 초과할 수 없습니다.

---

**다음**: [정산 & 상환](resolve-redeem.md) · [이벤트](events.md)
