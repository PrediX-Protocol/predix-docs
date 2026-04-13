---
description: 専用AMMプールなしでNOトークンの価格を設定する方法
---

# 仮想NO価格設定

NOトークンには専用のAMMプールがありません。RouterがYES/USDCプールとスプリット/マージ操作を使用してNO取引を合成します。

## NO購入

```
1. USDCをスプリット → YES + NO
2. YESをAMMプールで売却
3. NOトークンを保持

ユーザーはUSDCを支払い → NOトークンを受領
```

## NO売却

```
1. AMMプールからYESを購入
2. YES + NOをマージ → USDC
3. USDCをユーザーに返還

ユーザーはNOトークンを支払い → USDCを受領
```

## 価格関係

スプリット/マージアンカーにより：

```
NO価格 ≈ $1.00 - YES価格

YESが$0.70で取引される場合：
  NO ≈ $0.30
```

これはアービトラージによって強制されます — どんな乖離も価格を戻す利益機会を生み出します。

## ユーザーの視点から

Routerが内部的にすべての複雑さを処理します。ユーザーは単に呼び出すだけです：

```typescript
// NO購入 — YES購入と同じように
await router.buyNo(marketId, usdcIn, mintAmount, minNoOut, recipient, deadline);

// NO売却 — YES売却と同じように
await router.sellNo(marketId, noIn, minUsdcOut, recipient, deadline);
```

> ⚠️ **ガスコスト**: NO取引は追加ステップ（スプリット+スワップまたはスワップ+マージ）を含むため、ガスはYES取引の約2.5倍高くなります。CLOBはこのオーバーヘッドなしに直接NO取引をサポートしています。

---

**次へ**: [成行注文](market-orders.md) · [スマートルーティング](smart-routing.md) · [手数料](../concepts/fees.md)
