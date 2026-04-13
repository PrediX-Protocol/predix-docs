---
description: Uniswap v4 Hook — 動的手数料、サンドイッチ攻撃防止、プール管理
---

# Hook コントラクト

PrediXHookV1は、AMMプールに予測市場固有のロジックを追加するUniswap v4 Hookです。

## Hook権限

| Hookポイント | 有効 | 目的 |
| ---------- | ------- | ------- |
| `beforeInitialize` | ✅ | プール登録の検証 |
| `afterInitialize` | ✅ | プールメタデータの設定 |
| `beforeSwap` | ✅ | 動的手数料 + サンドイッチ攻撃チェック |
| `afterSwap` | ✅ | スワップデータの記録、イベント発行 |

## 動的手数料計算

マーケットの期限が近づくにつれて手数料が増加し、情報を持つトレーダーからLPを保護します：

| 残り時間 | 手数料 |
| -------------- | --- |
| > 7日 | 0.5% |
| 3〜7日 | 1.0% |
| 1〜3日 | 2.0% |
| < 24時間 | 5.0% |

## サンドイッチ攻撃防止

Hookは各スワップをユーザーごとプールごとに`(block.number, direction)`として追跡します。同じユーザーが同じブロック内で逆方向にスワップした場合、`SandwichDetected()`エラーでトランザクションがリバートされます。

**信頼されたRouter**：Hookは`msg.sender`の代わりに`hookData`から実際のユーザーアドレスを抽出するため、Routerがユーザーの代わりに実行しても誤検知が発生しません。

## 関数

```solidity
// Diamondのみ呼び出し可能
registerMarketPool(bytes32 marketId, address yesToken, PoolKey key)
setTrustedRouter(address router, bool trusted)

// デプロイ時に一度だけ呼び出し
initialize(address diamond, address usdc, address admin)

// ビュー
getMarketForPool(PoolId poolId) → bytes32 marketId
```

---

**次へ**: [Oracle](oracle.md) · [手数料](../concepts/fees.md) · [安全機構](safety.md)
