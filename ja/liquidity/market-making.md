---
description: CLOBマーケットメイキング — オーダーブック流動性の提供
---

# マーケットメイキング

## 概要

CLOBでのマーケットメイキングは、スプレッドを獲得するために買い注文と売り注文を同時に配置することです。

## 基本戦略

```typescript
const exchange = new ethers.Contract(EXCHANGE_ADDRESS, EXCHANGE_ABI, signer);

// 買い(BUY_YES)と売り(SELL_YES)を配置
const bidPrice = ethers.parseUnits("0.48", 6);
const askPrice = ethers.parseUnits("0.52", 6);
const amount = ethers.parseUnits("100", 6);

// BUY_YES: USDCを預入
await exchange.placeOrder(marketId, 0, bidPrice, amount); // side 0 = BUY_YES

// SELL_YES: YESトークンを預入（分割で保有している必要あり）
await exchange.placeOrder(marketId, 1, askPrice, amount); // side 1 = SELL_YES
```

## スプレッド管理

| 市場フェーズ | 推奨スプレッド |
| ------------ | ------------------ |
| 新規市場（低取引量） | $0.05–$0.10 |
| アクティブ市場 | $0.02–$0.05 |
| 満期間近（< 24時間） | $0.05–$0.10またはそれ以上 |

## 在庫リスク

一方の約定が他方より多くなると、方向性のエクスポージャーが蓄積されます:

- YESトークンが多すぎる → YESが勝てば利益
- NOトークンが多すぎる → NOが勝てば利益

価格の調整やsplit/mergeを使用してリバランスしてください。

## マーケットメイカーボット

PrediXはリファレンスとなるマーケットメイカーボットの実装を提供しています。詳細はGitHubリポジトリを参照してください。

---

**次へ**: [流動性の概要](overview.md) · [指値注文](../trading/limit-orders.md)
