---
description: ロールベースアクセス制御 — きめ細かい権限を持つ4つのロール
---

# アクセス制御

## ロール

| ロール | ID | 目的 |
| ---- | -- | ------- |
| DEFAULT_ADMIN | 0 | すべてのロールを管理 |
| ADMIN | 1 | マーケット作成、手数料設定、Oracle承認 |
| OPERATOR | 2 | 緊急結果確定、返金の有効化 |
| PAUSER | 3 | システムの一時停止 |

## 権限マトリクス

| 関数 | PUBLIC | ADMIN | OPERATOR | PAUSER | OWNER |
| -------- | ------ | ----- | -------- | ------ | ----- |
| `createMarket` | | ✓ | | | |
| `splitPosition` | ✓ | | | | |
| `mergePositions` | ✓ | | | | |
| `resolveMarket` | ✓ | | | | |
| `redeemMarketTokens` | ✓ | | | | |
| `placeOrder` | ✓ | | | | |
| `buyYes` (Router) | ✓ | | | | |
| `emergencyResolve` | | | ✓ (7日間) | | |
| `enableRefundMode` | | | ✓ | | |
| `setTvlCap` | | ✓ | | | |
| `setApprovedOracle` | | ✓ | | | |
| `pauseAll` | | | | ✓ | |
| `diamondCut` | | | | | ✓ |

## 2段階所有権移転

```
1. 現在のオーナーが transferOwnership(newOwner) を呼び出す
2. newOwner が pendingOwner になる
3. newOwner が acceptOwnership() を呼び出す
4. 所有権の移転が完了
```

これにより、誤ったアドレスへの意図しない移転を防止します。

## 最終管理者保護

システムは最後の管理者のロール取り消しを防止し、プロトコルに常に少なくとも1人の管理者がいることを保証します。

---

**次へ**: [安全機構](safety.md) · [Diamond コントラクト](diamond.md)
