---
description: USDCをトークンに分割し、トークンを再統合する
---

# 分割 & マージ（開発者ガイド）

## ポジションの分割

```typescript
const diamond = new ethers.Contract(DIAMOND_ADDRESS, DIAMOND_ABI, signer);
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

const marketId = "0x...";
const amount = ethers.parseUnits("100", 6); // 100 USDC

// DiamondにUSDCを承認
await (await usdc.approve(DIAMOND_ADDRESS, amount)).wait();

// 分割: 100 USDC → 100 YES + 100 NO
const tx = await diamond.splitPosition(marketId, amount);
await tx.wait();

// 残高を確認
const market = await diamond.getMarket(marketId);
const yesToken = new ethers.Contract(market.yesToken, ERC20_ABI, provider);
const noToken = new ethers.Contract(market.noToken, ERC20_ABI, provider);

console.log("YES 残高:", ethers.formatUnits(await yesToken.balanceOf(signer.address), 6));
console.log("NO 残高:", ethers.formatUnits(await noToken.balanceOf(signer.address), 6));
```

## ポジションのマージ

```typescript
const mergeAmount = ethers.parseUnits("50", 6); // 50 YES + 50 NOをマージ

// 承認不要（Diamondは既にバーン権限を保有）
const tx = await diamond.mergePositions(marketId, mergeAmount);
await tx.wait();

// 結果: 50 USDCがウォレットに返却されます
```

> ⚠️ マージするには**同量**のYESおよびNOトークンを保有する必要があります。マージ量はどちらのトークンの残高も超えることはできません。

---

**次へ**: [解決 & 償還](resolve-redeem.md) · [イベント](events.md)
