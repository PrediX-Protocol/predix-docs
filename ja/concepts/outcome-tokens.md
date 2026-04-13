---
description: YESおよびNOトークン — 完全なDeFiコンポーザビリティを備えたERC-20標準
---

# アウトカムトークン

## 概要

各PrediXマーケットは2つの**ERC-20**アウトカムトークンを作成します：

| トークン | $1.00が支払われる条件 | $0.00が支払われる条件 |
| -------- | --------------------- | --------------------- |
| **YES** | イベントが発生 | イベントが発生しない |
| **NO** | イベントが発生しない | イベントが発生 |

## 技術仕様

| プロパティ | 値 |
| ---------- | -- |
| 標準 | ERC-20 + ERC-2612 (Permit) |
| 小数点 | 6（USDCと同じ） |
| 発行権限 | Diamondコントラクトのみ（`onlyFactory`修飾子） |
| バーン権限 | Diamondコントラクトのみ |
| 不変フィールド | `factory`, `marketId`, `isYesToken` |

## ERC-2612 Permit

アウトカムトークンは`permit`関数によるガスレス承認をサポートしています。これにより、オフチェーン署名を使用して単一トランザクションで承認と転送を実行できます。

```typescript
// オフチェーンでpermitに署名
const deadline = Math.floor(Date.now() / 1000) + 3600;
const nonce = await yesToken.nonces(userAddress);
const signature = await signer.signTypedData(domain, types, { owner, spender, value, nonce, deadline });

// オンチェーンでpermitを実行（事前のapprove不要）
await yesToken.permit(owner, spender, value, deadline, v, r, s);
```

## コア不変条件

```
YES.totalSupply == NO.totalSupply == market.totalCollateral
```

この不変条件は**すべて**の操作で維持されます：`splitPosition`、`mergePositions`、`groupSplit`、`groupMerge`、`redeemMarketTokens`、`refund`。

## アドレス生成

YESトークンアドレスはUSDCアドレスよりも小さくなければなりません（Uniswap v4プール順序要件）。PrediXは**CREATE2ソルトマイニング**を使用して、準拠するアドレスを決定論的に見つけます。

> ⚠️ **重要**: アウトカムトークンはDiamondコントラクトによってのみ発行およびバーンできます。パブリックなミント関数はありません。

---

**次へ**: [スプリット＆マージ](split-and-merge.md) · [手数料](fees.md) · [取引概要](../trading/overview.md)
