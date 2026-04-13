---
description: Smart Routerを通じた成行注文の実行
---

# 成行注文

成行注文はRouterを通じて即座に実行され、CLOBとAMMの両方の流動性を集約します。

## 関数

| 関数 | アクション |
| ---- | ---------- |
| `buyYes(marketId, usdcIn, minYesOut, recipient, deadline)` | USDCを支払い、YESトークンを受領 |
| `sellYes(marketId, yesIn, minUsdcOut, recipient, deadline)` | YESトークンを支払い、USDCを受領 |
| `buyNo(marketId, usdcIn, mintAmount, minNoOut, recipient, deadline)` | USDCを支払い、NOトークンを受領 |
| `sellNo(marketId, noIn, minUsdcOut, recipient, deadline)` | NOトークンを支払い、USDCを受領 |

## コード例

### YES購入

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
const minYesOut = ethers.parseUnits("90", 6);   // 2%スリッページ
const deadline = Math.floor(Date.now() / 1000) + 300; // 5分

// ステップ1：USDCをRouterに承認
await usdc.approve(router.target, usdcIn);

// ステップ2：YES購入
const tx = await router.buyYes(marketId, usdcIn, minYesOut, signer.address, deadline);
const receipt = await tx.wait();
console.log("取引約定:", receipt.hash);
```

### YES売却

```typescript
const yesToken = new ethers.Contract(market.yesToken, ERC20_ABI, signer);
const yesIn = ethers.parseUnits("100", 6);
const minUsdcOut = ethers.parseUnits("60", 6);

// YESトークンをRouterに承認
await yesToken.approve(router.target, yesIn);

// YES売却
await router.sellYes(marketId, yesIn, minUsdcOut, signer.address, deadline);
```

### NO購入

```typescript
// NO購入にはmintAmountパラメータが必要（内部でスプリットに使用）
const usdcIn = ethers.parseUnits("100", 6);
const mintAmount = ethers.parseUnits("100", 6); // スプリットする数量
const minNoOut = ethers.parseUnits("90", 6);

await usdc.approve(router.target, usdcIn);
await router.buyNo(marketId, usdcIn, mintAmount, minNoOut, signer.address, deadline);
```

## 取引前の見積もり

実行前に必ず見積もりを取得してください：

```typescript
const expectedYes = await router.quoteBuyYes(marketId, usdcIn);
console.log("予想YES:", ethers.formatUnits(expectedYes, 6));

// スリッページ許容範囲を設定したminOut
const minYesOut = expectedYes * 98n / 100n; // 2%スリッページ
```

---

**次へ**: [指値注文](limit-orders.md) · [スマートルーティング](smart-routing.md) · [開発者取引統合](../developers/trading-integration.md)
