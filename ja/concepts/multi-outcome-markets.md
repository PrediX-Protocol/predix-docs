---
description: カテゴリ — 2つ以上の結果を持つマーケット
---

# マルチアウトカムマーケット

## カテゴリ

**カテゴリ**は、複数のYES/NOマーケットを1つのマルチアウトカムイベントにグループ化します。

### 例：「2028年米国大統領選挙で誰が勝つか？」

```
Category: "US President 2028"
  Market 0: "Trump wins?"     → YES/NO トークン
  Market 1: "DeSantis wins?"  → YES/NO トークン
  Market 2: "Newsom wins?"    → YES/NO トークン
  Market 3: "Harris wins?"    → YES/NO トークン
  Market 4: "Other wins?"     → YES/NO トークン
```

## GroupSplit & GroupMerge

### GroupSplit

`amount × N` USDC（N = カテゴリ内のマーケット数）を預け入れて、各マーケットごとに`amount`個のYES + NOを受け取ります。

```typescript
// カテゴリに5つのマーケット。500 USDC預入 → マーケットごとに100 YES+NO
const amount = ethers.parseUnits("100", 6);
// 100 × 5 = 500 USDCの承認が必要
await usdc.approve(DIAMOND_ADDRESS, ethers.parseUnits("500", 6));
await diamond.groupSplit(categoryId, amount);
```

### GroupMerge

すべてのマーケットから`amount`個のYES + NOをバーンして`amount × N` USDCを回収します。

```typescript
await diamond.groupMerge(categoryId, amount);
// 5つのマーケットそれぞれから100 YES + 100 NOをバーン
// 500 USDCを返還
```

## 結果確定

```typescript
// 勝者は1つ：インデックス2のマーケットが勝利
await diamond.resolveCategory(categoryId, 2);

// 結果：
// Market 2 → YES勝利（YES保有者が$1を償還）
// Markets 0, 1, 3, 4 → NO勝利（NO保有者が$1を償還）
// すべてアトミックに結果確定
```

> ⚠️ **重要**: `resolveCategory`はADMINロールによって呼び出され、カテゴリ内のすべてのマーケットを同時に結果確定します。

---

**次へ**: [手数料](fees.md) · [取引概要](../trading/overview.md) · [マーケット作成（開発者向け）](../developers/create-market.md)
