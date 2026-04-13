---
description: 一般的なエラーとその解決方法
---

# エラーリファレンス

## 一般的なエラー

| エラー | 原因 | 解決方法 |
| ----- | ----- | -------- |
| `Market_NotFound` | 無効なmarketId | MarketCreatedイベントでmarketIdを確認 |
| `Market_Ended` | マーケットのendTimeが経過 | 期限後は取引不可 |
| `Market_ExceedsPerTradeCap` | 取引あたりの安全上限を超過 | 取引サイズを縮小 |
| `Market_ExceedsTvlCap` | システム全体のTVL上限に到達 | 待機するか少額で実行 |
| `Market_ExceedsPerMarketCap` | マーケットの担保上限に到達 | 少額で実行 |
| `InvalidPrice` | 価格が$0.01ティックグリッドに合っていない | `10000`（6桁小数）の倍数を使用 |
| `MaxOrdersExceeded` | このマーケットでユーザーの注文が50件 | 既存の注文を先にキャンセル |
| `MarketNotActive` | マーケットが期限切れ、解決済み、または返金モード | マーケットの状態を確認 |
| `InsufficientBalance` | 操作に必要なトークンが不足 | トークン残高を確認 |
| `SandwichDetected` | 同じブロックで逆方向のスワップ | 次のブロックまで待機 |
| `SlippageExceeded` | 出力がminOut未満 | スリッページを増やすか再試行 |
| `DeadlineExpired` | デッドライン後にトランザクションがマイニング | より長いデッドラインを設定 |
| `OnlyFactory` | トークンの直接ミント/バーンを試行 | トークンはDiamondを通じてのみミント可能 |

## デバッグのヒント

```typescript
try {
  await router.buyYes(marketId, usdcIn, minYesOut, recipient, deadline);
} catch (error: any) {
  // カスタムエラーをデコード
  const iface = new ethers.Interface(ROUTER_ABI);
  try {
    const decoded = iface.parseError(error.data);
    console.error("カスタムエラー:", decoded?.name, decoded?.args);
  } catch {
    console.error("生エラー:", error.message);
  }
}
```

---

**次へ**: [開発者クイックスタート](quickstart.md) · [取引統合](trading-integration.md)
