---
description: AMM流動性提供のステップバイステップガイド
---

# 流動性の提供

## 概要

AMM流動性はPositionManagerを通じてUniswap v4 YES/USDCプールに提供されます。

## 手順

### 1. USDCをYES + NOに分割

```typescript
const amount = ethers.parseUnits("1000", 6); // 1000 USDC
await usdc.approve(DIAMOND_ADDRESS, amount);
await diamond.splitPosition(marketId, amount);
// これで1000 YES + 1000 NOを保有します
```

### 2. YESとUSDCをPositionManagerに承認

```typescript
const yesToken = new ethers.Contract(market.yesToken, ERC20_ABI, signer);
await yesToken.approve(POSITION_MANAGER, ethers.MaxUint256);
await usdc.approve(POSITION_MANAGER, ethers.MaxUint256);
```

### 3. LPポジションのミント

Uniswap v4 PositionManagerを使用してYES/USDCプールに流動性ポジションを作成します。以下から選択できます:

- **フルレンジ**: $0.01–$0.99をカバー、資本効率が低い
- **集中**: 狭い範囲をカバー（例: $0.40–$0.70）、資本効率が高い

### 4. 除去のタイミング

- 市場満期の**少なくとも24時間前**にLPを除去（手数料が5%に急騰する時）
- 市場を監視する — 結果が早期に確実になった場合、すぐに除去する
- 決済後、敗北トークンの価値は$0です

> ⚠️ **注意**: 分割で得たNOトークンは別途売却または保有できます。LPには必要ありません。

---

**次へ**: [マーケットメイキング](market-making.md) · [流動性の概要](overview.md)
