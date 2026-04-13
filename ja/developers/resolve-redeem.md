---
description: マーケットの解決と勝利トークンの償還
---

# 解決 & 償還

## マーケットの解決

オラクルが結果を報告した後、誰でも確定できます:

```typescript
// オラクルが報告したか確認
const oracle = new ethers.Contract(ORACLE_ADDRESS, ORACLE_ABI, provider);
const [resolved, outcome] = await oracle.getResolution(marketId);

if (resolved) {
  const tx = await diamond.resolveMarket(marketId);
  await tx.wait();
  console.log("マーケット解決完了。結果:", outcome ? "YES" : "NO");
}
```

## 勝利トークンの償還

```typescript
// 勝者は勝利トークン1つにつき1 USDCを受け取ります
const tx = await diamond.redeemMarketTokens(marketId);
await tx.wait();
```

## 緊急解決（OPERATOR専用）

```typescript
// オラクルが報告していない場合、endTimeから7日後に利用可能
await diamond.emergencyResolve(marketId, true); // true = YES勝利
```

## 返金モード（OPERATOR専用）

```typescript
// 問題のあるマーケットの返金を有効化
await diamond.enableRefundMode(marketId);

// ユーザーが比例返金を請求
await diamond.refund(marketId);
```

---

**次へ**: [イベント](events.md) · [エラー](errors.md)
