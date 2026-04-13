---
description: プラガブルオラクルシステム — Manual、Chainlink、カスタムアダプター
---

# Oracle システム

PrediXはプラガブルオラクルアーキテクチャを採用しています。すべてのオラクルは同じインターフェースを実装します：

```solidity
interface IOracle {
    function getResolution(bytes32 marketId)
        external view returns (bool resolved, bool outcome);
}
```

## ManualOracleAdapter

主観的なイベント（政治、スポーツなど）のための管理者制御による結果確定。

```solidity
resolve(bytes32 marketId, bool outcome)     // 管理者のみ
getResolution(bytes32 marketId) → (bool resolved, bool outcome)
getResolvedAt(bytes32 marketId) → uint64
```

## ChainlinkAdapter

Chainlink価格フィードに基づく自動結果確定。例：「BTC > $100K？」

**安全チェック**：古いフィード検出（24時間）、L2シーケンサー稼働時間、ラウンド完全性。

## カスタムOracleの作成

1. `IOracle`インターフェースを実装
2. アダプターコントラクトをデプロイ
3. 管理者が承認：`diamond.setApprovedOracle(adapterAddress, true)`
4. マーケット作成時に使用：`createMarket(..., adapterAddress)`

```solidity
contract MyCustomOracle is IOracle {
    mapping(bytes32 => bool) public resolved;
    mapping(bytes32 => bool) public outcomes;

    function getResolution(bytes32 marketId)
        external view returns (bool, bool)
    {
        return (resolved[marketId], outcomes[marketId]);
    }

    function resolve(bytes32 marketId, bool outcome) external {
        // ここに結果確定ロジックを記述
        resolved[marketId] = true;
        outcomes[marketId] = outcome;
    }
}
```

> ⚠️ **Oracle承認**：マーケット作成時には管理者が承認したOracleアドレスのみ使用できます。

---

**次へ**: [アクセス制御](access-control.md) · [安全機構](safety.md) · [結果確定の概念](../concepts/resolution.md)
