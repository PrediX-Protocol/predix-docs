---
description: スマート Router — Uniswap v4 によるハイブリッド CLOB + AMM 実行
---

# Router コントラクト

PrediXRouterは、CLOB（Exchange）とAMM（Uniswap v4）の流動性を統合し、トレーダーに最良の約定を提供します。

## トレーディング関数

```solidity
buyYes(bytes32 marketId, uint256 usdcIn, uint256 minYesOut,
       address recipient, uint256 deadline) → uint256 yesOut

sellYes(bytes32 marketId, uint256 yesIn, uint256 minUsdcOut,
        address recipient, uint256 deadline) → uint256 usdcOut

buyNo(bytes32 marketId, uint256 usdcIn, uint256 mintAmount,
      uint256 minNoOut, address recipient, uint256 deadline) → uint256 noOut

sellNo(bytes32 marketId, uint256 noIn, uint256 minUsdcOut,
       address recipient, uint256 deadline) → uint256 usdcOut
```

## 見積もり関数

```solidity
quoteBuyYes(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellYes(bytes32 marketId, uint256 yesIn) → uint256
quoteBuyNo(bytes32 marketId, uint256 usdcIn) → uint256
quoteSellNo(bytes32 marketId, uint256 noIn) → uint256
```

## ルーティングロジック

```
buyYes($100):
  1. AMM価格より有利なCLOBのSELL_YES注文を確認
  2. CLOBで約定（スリッページなし、指値）
  3. 残りをUniswap v4 AMMスワップにルーティング
  4. 合計YESを受取人に返却
```

## 仮想NO価格設定

YES/USDCプールのみ存在するため：

- **NO購入**：USDCをYES+NOに分割 → AMMでYESを売却 → NOを保持
- **NO売却**：AMMでYESを購入 → YES+NOを統合 → USDCを返却

## Flash Accounting

RouterはUniswap v4のFlash Accounting（unlockコールバックパターン）を使用して、ガス効率の良い多段階操作を実行します。

> ⚠️ **パラメータ**：常に`deadline`（通常5分）と`minOut`（スリッページ保護）を設定してください。条件が満たされない場合、トランザクションはリバートされます。

---

**次へ**: [Hook](hook.md) · [スマートルーティング](../trading/smart-routing.md) · [成行注文](../trading/market-orders.md)
