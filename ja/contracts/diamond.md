---
description: Diamond プロキシ (EIP-2535) — マーケット管理、分割、統合、結果確定、償還
---

# Diamond コントラクト

Diamondは PrediX Protocol の中央コントラクトであり、モジュール式アップグレードのために EIP-2535 を実装しています。

## Facet一覧

| Facet | 関数 |
| ----- | --------- |
| **DiamondCutFacet** | `diamondCut` — Facetの追加/置換/削除 |
| **DiamondLoupeFacet** | `facets`, `facetAddresses`, `facetFunctionSelectors` — イントロスペクション |
| **AccessControlFacet** | `grantRole`, `revokeRole`, `hasRole` |
| **OwnableFacet** | `transferOwnership`, `acceptOwnership`, `owner` |
| **PausableFacet** | `pause`, `unpause`, `paused` |
| **MarketFacet** | すべてのマーケット操作（以下参照） |

## 動作の仕組み

Diamondへのすべての関数呼び出しはセレクターによってルーティングされます：

```
ユーザーが diamond.splitPosition(marketId, amount) を呼び出す
  → Diamond fallback: セレクター 0x... を検索 → MarketFacet アドレス
  → MarketFacet に delegatecall
  → MarketFacet が Diamond のストレージを使用して実行
```

## MarketFacet 関数

### 作成 (ADMIN_ROLE)

```solidity
createMarket(string question, string description, uint256 endTime,
             bytes32 categoryId, address oracle) → bytes32 marketId

createCategory(string name, string description, uint256 endTime) → bytes32 categoryId

addMarketToCategory(bytes32 categoryId, bytes32 marketId)
removeMarketFromCategory(bytes32 categoryId, bytes32 marketId)
```

### ポジション管理 (PUBLIC)

```solidity
splitPosition(bytes32 marketId, uint256 amount)
mergePositions(bytes32 marketId, uint256 amount)
groupSplit(bytes32 categoryId, uint256 amount)
groupMerge(bytes32 categoryId, uint256 amount)
```

### 結果確定

```solidity
resolveMarket(bytes32 marketId)                              // PUBLIC（オラクル後）
resolveCategory(bytes32 categoryId, uint256 winningIndex)    // ADMIN_ROLE
emergencyResolve(bytes32 marketId, bool outcome)             // OPERATOR_ROLE（7日間遅延）
enableRefundMode(bytes32 marketId)                           // OPERATOR_ROLE
refund(bytes32 marketId)                                     // PUBLIC（返金モード）
```

### 償還 (PUBLIC)

```solidity
redeemMarketTokens(bytes32 marketId)
redeemCategoryTokens(bytes32 categoryId)
```

### ビュー関数

```solidity
getMarket(bytes32 marketId) → MarketData
getCategory(bytes32 categoryId) → CategoryData
isMarketExists(bytes32 marketId) → bool
isRefundMode(bytes32 marketId) → bool
getSafetyCaps() → (uint256 tvlCap, uint256 perTradeCap, uint256 perMarketCap)
```

### 管理者 (ADMIN_ROLE)

```solidity
setApprovedOracle(address oracle, bool approved)
setTvlCap(uint256 cap)
setPerTradeCap(uint256 cap)
setPerMarketCap(uint256 cap)
```

## MarketData 構造体

```solidity
struct MarketData {
    string question;
    string description;
    uint256 endTime;
    address yesToken;
    address noToken;
    PoolId poolId;
    bytes32 categoryId;
    address oracle;
    bool isResolved;
    bool outcome;           // true = YES勝利、false = NO勝利
    uint256 totalCollateral;
    bytes32 questionHash;   // 非推奨
    uint256 resolvedAt;
}
```

> ⚠️ **重要なセレクター** (`diamondCut`, `owner`, `transferOwnership`) は保護されており、削除できません。

---

**次へ**: [Exchange](exchange.md) · [Router](router.md) · [アクセス制御](access-control.md)
