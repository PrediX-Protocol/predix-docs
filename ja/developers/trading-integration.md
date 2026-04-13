---
description: アプリケーションにPrediX取引を統合する
---

# 取引統合

## ステップ1: トークンの承認

```typescript
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

// 成行注文のためにRouterを承認
await usdc.approve(ROUTER_ADDRESS, ethers.MaxUint256);

// 指値注文のためにExchangeを承認
await usdc.approve(EXCHANGE_ADDRESS, ethers.MaxUint256);

// 分割/マージのためにDiamondを承認
await usdc.approve(DIAMOND_ADDRESS, ethers.MaxUint256);
```

## ステップ2: 成行注文（Router経由）

```typescript
const router = new ethers.Contract(ROUTER_ADDRESS, ROUTER_ABI, signer);
const marketId = "0x...";
const deadline = Math.floor(Date.now() / 1000) + 300;

// まず見積もりを取得
const usdcIn = ethers.parseUnits("100", 6);
const expectedYes = await router.quoteBuyYes(marketId, usdcIn);
const minYesOut = expectedYes * 98n / 100n; // 2% スリッページ

// 実行
const tx = await router.buyYes(marketId, usdcIn, minYesOut, signer.address, deadline);
await tx.wait();
```

## ステップ3: 指値注文（Exchange経由）

```typescript
const exchange = new ethers.Contract(EXCHANGE_ADDRESS, EXCHANGE_ABI, signer);

// $0.65でYESを購入
const side = 0; // BUY_YES
const price = ethers.parseUnits("0.65", 6);
const amount = ethers.parseUnits("100", 6);

const tx = await exchange.placeOrder(marketId, side, price, amount);
const receipt = await tx.wait();
```

## ステップ4: 注文のキャンセル

```typescript
const orderId = "0x..."; // OrderPlacedイベントから取得
await exchange.cancelOrder(orderId);
```

## エラー処理

```typescript
try {
  await router.buyYes(marketId, usdcIn, minYesOut, signer.address, deadline);
} catch (error: any) {
  if (error.message.includes("Market_NotFound")) {
    console.error("無効なマーケットID");
  } else if (error.message.includes("Market_Ended")) {
    console.error("マーケットは期限切れです");
  } else if (error.message.includes("Market_ExceedsPerTradeCap")) {
    console.error("取引あたりの上限を超えています");
  } else if (error.message.includes("SlippageExceeded")) {
    console.error("価格が大きく変動しました。スリッページを増やしてください");
  }
}
```

---

**次へ**: [分割 & マージ](split-merge.md) · [解決 & 償還](resolve-redeem.md) · [イベント](events.md)
