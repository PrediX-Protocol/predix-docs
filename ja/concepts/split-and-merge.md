---
description: USDCをアウトカムトークンにスプリット、またはマージして戻す
---

# スプリット＆マージ

スプリットとマージは、アウトカムトークンの価格を実際の価値に固定する基本的なメカニズムです。

## スプリット

USDC担保を預け入れて、同数のYESおよびNOトークンを受け取ります：

```
1 USDC → 1 YES + 1 NO
```

担保はDiamondコントラクトにロックされます。両方のトークン供給量が均等に増加します。

## マージ

同数のYESおよびNOトークンをバーンしてUSDCを引き出します：

```
1 YES + 1 NO → 1 USDC
```

担保はDiamondからリリースされます。両方のトークン供給量が均等に減少します。

## これが重要な理由

スプリット/マージはマーケット全体の**価格アンカー**を作成します：

```
YES = $0.70でNO = $0.40の場合：
  YES + NO = $1.10 > $1.00
  → アービトラージャー：$1をスプリット → YESを$0.70で売却 + NOを$0.40で売却 = $1.10
  → 利益：$0.10
  → 売り圧力が価格を合計 ≈ $1.00に戻す
```

## コード例

```typescript
import { ethers } from "ethers";

const diamond = new ethers.Contract(DIAMOND_ADDRESS, DIAMOND_ABI, signer);
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

const marketId = "0x...";
const amount = ethers.parseUnits("100", 6); // 100 USDC

// ステップ1：USDCをDiamondに承認
await usdc.approve(DIAMOND_ADDRESS, amount);

// ステップ2：スプリット — 100 YES + 100 NOを受領
await diamond.splitPosition(marketId, amount);

// ステップ3：マージ — 100 YES + 100 NOをバーン、100 USDCを受領
await diamond.mergePositions(marketId, amount);
```

## 安全上限

スプリット操作には安全上限が適用されます：

| 上限 | 説明 |
| ---- | ---- |
| **TVL上限** | システムにロックできる最大USDC |
| **取引あたりの上限** | 単一スプリット操作あたりの最大USDC |
| **マーケットあたりの上限** | 個別マーケットあたりの最大USDC |

> ⚠️ **注意**: マージするには同数のYESとNOトークンを保有する必要があります。マージ数量はどちらかのトークン残高を超えることはできません。

---

**次へ**: [結果確定](resolution.md) · [マルチアウトカムマーケット](multi-outcome-markets.md) · [取引概要](../trading/overview.md)
