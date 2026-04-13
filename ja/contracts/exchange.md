---
description: 99ティックビットマップと3段階マッチングを備えたオンチェーン CLOB
---

# Exchange コントラクト

PrediXExchangeは、ガス効率の良い価格スキャンのための99ティックビットマップを備えたオンチェーンCLOB（Central Limit Order Book）を実装しています。

## コア関数

### 注文の発注

```solidity
placeOrder(bytes32 marketId, uint8 side, uint256 price, uint256 amount)
    → (bytes32 orderId, uint256 filledAmount)
```

既存の注文とのマッチングを試みます。未約定部分はレスティング注文になります。

### 注文のキャンセル

```solidity
cancelOrder(bytes32 orderId)
```

所有者はいつでもキャンセルできます。マーケット期限後は誰でもキャンセルできます。

### 成行注文の約定

```solidity
fillMarketOrder(bytes32 marketId, uint8 side, uint256 maxPrice,
                uint256 amount, address taker, address recipient)
    → (uint256 filledAmount, uint256 totalCost)
```

ハイブリッド実行のCLOB部分のためにRouterが使用します。

## ビュー関数

```solidity
getOrder(bytes32 orderId) → Order
getBestPrices(bytes32 marketId) → (bestBidYes, bestAskYes, bestBidNo, bestAskNo)
getDepthAtPrice(bytes32 marketId, uint8 side, uint256 price) → uint256
getOrderBook(bytes32 marketId, uint8 depth) → (yesBids, yesAsks, noBids, noAsks)
```

## マッチングアルゴリズム

注文は3段階でマッチングされます：

1. **COMPLEMENTARY**：直接の相手方（BUY_YES ↔ SELL_YES）
2. **MINT**：2つの買い注文（BUY_YES + BUY_NO、合計 ≥ $1.00）
3. **MERGE**：2つの売り注文（SELL_YES + SELL_NO、合計 ≤ $1.00）

## 制約条件

| パラメータ | 値 |
| --------- | ----- |
| 価格範囲 | `10000` – `990000`（$0.01 – $0.99、6桁） |
| 価格刻み | `10000`（$0.01） |
| 最小注文 | `1000000`（$1.00） |
| ユーザーあたりマーケットあたり最大注文数 | 50 |
| 注文あたり最大約定数 | 20 |

## 一時停止メカニズム

```solidity
pause()            // PAUSE_ADMIN のみ
unpause()          // PAUSE_ADMIN のみ
setPauseAdmin(address) // 現在の PAUSE_ADMIN のみ
```

---

**次へ**: [Router](router.md) · [マッチングエンジン](../trading/matching-engine.md) · [指値注文](../trading/limit-orders.md)
