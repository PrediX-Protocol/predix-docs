---
description: マーケットの結果確定方法と勝者の償還手順
---

# 結果確定

## マーケットライフサイクル

```
作成済み → アクティブ（取引中） → 満了 → 結果確定済み → 償還可能
                                   ↓
                           緊急（7日間） → 緊急結果確定
                                   ↓
                           返金モード → ユーザー返金
```

## 通常の結果確定

1. マーケットが`endTime`に到達（取引は継続するが新しいスプリットは不可）
2. オラクルが結果を報告：`resolve(marketId, true/false)`
3. 誰でもDiamond上で`resolveMarket(marketId)`を呼び出し可能
4. マーケットが結果確定済みとしてマーク
5. 勝者が`redeemMarketTokens(marketId)`を呼び出し

```typescript
// ステップ1：結果確定（オラクル報告後、誰でも呼び出し可能）
await diamond.resolveMarket(marketId);

// ステップ2：勝利トークンの償還
await diamond.redeemMarketTokens(marketId);
// 勝者は勝利トークンあたり1 USDCを受け取る
```

## 緊急結果確定

`endTime`後7日以内にオラクルが報告しない場合：

1. `OPERATOR`が`emergencyResolve(marketId, outcome)`を呼び出し可能
2. オラクルをバイパスして結果を直接設定

> ⚠️ 緊急結果確定には`endTime`後**7日間の遅延**が必要であり、OPERATORロールのみが呼び出せます。

## 返金モード

マーケットが無効または問題があると判断された場合：

1. `OPERATOR`が`enableRefundMode(marketId)`を呼び出し
2. すべての取引がブロック（スプリット、マージ、CLOB、AMM）
3. トークン保有者が`refund(marketId)`を呼び出し
4. 各保有者はYES + NO残高に比例してUSDCを受け取る

```typescript
// オペレーターが返金を有効化
await diamond.enableRefundMode(marketId);

// ユーザーが比例返金を請求
await diamond.refund(marketId);
```

## カテゴリ結果確定

マルチアウトカムマーケットの場合：

```typescript
// 管理者がカテゴリを結果確定：勝者はアウトカムインデックス2
await diamond.resolveCategory(categoryId, 2);
// カテゴリ内のすべてのマーケットがアトミックに結果確定
```

---

**次へ**: [マルチアウトカムマーケット](multi-outcome-markets.md) · [手数料](fees.md) · [オラクルコントラクト](../contracts/oracle.md)
