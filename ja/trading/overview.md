---
description: 2つの取引所 — CLOBとAMM — Smart Routerで統合
---

# 取引概要

PrediXはSmart Routerで統合された2つの取引所を提供します：

```
User → PrediXRouter (Smart Router)
          ├── PrediXExchange (CLOB) — 指値注文、スリッページなし
          └── Uniswap v4 AMM — 常時流動性提供
```

## 成行注文 vs 指値注文

| タイプ | 経由 | 最適な用途 |
| ------ | ---- | ---------- |
| **成行注文** | Router (`buyYes`, `sellYes`, `buyNo`, `sellNo`) | 即時約定、最良価格 |
| **指値注文** | Exchange (`placeOrder`) | 特定価格、忍耐力のあるトレーダー |

## 取引フロー

1. USDC（またはトークン）をコントラクトに**承認**
2. `quoteBuyYes` / `quoteSellYes`などを使用して予想出力量を**照会**
3. スリッページ保護（`minOut`パラメータ）付きで取引を**実行**
4. アウトカムトークン（またはUSDC）を**受領**

> ⚠️ **常にデッドラインを設定してください**（通常は現在から5分後）そしてスリッページ保護のために**minOut**を設定してください。

---

**次へ**: [成行注文](market-orders.md) · [指値注文](limit-orders.md) · [スマートルーティング](smart-routing.md)
