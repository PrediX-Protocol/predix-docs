---
description: PrediX Protocolのアーキテクチャと主要な差別化要素の概要
---

# PrediXとは？

PrediX Protocolは、Uniswap v4上に構築され、Unichain L2にデプロイされた**分散型予測市場プラットフォーム**です。誰でも現実世界のイベントに対するマーケットを作成し、完全にトラストレスなオンチェーン環境でアウトカムトークンを取引できます。

## 主要な差別化要素

| 機能 | PrediX | その他 |
| ------- | ------ | ------ |
| **取引エンジン** | ハイブリッドCLOB + AMM + Smart Router | CLOBのみ（Polymarket）またはAMMのみ（Augur） |
| **アウトカムトークン** | ERC-20（すべてのDeFiと組み合わせ可能） | ERC-1155（限定的なコンポーザビリティ） |
| **インフラ** | Uniswap v4 Hooks + Flash Accounting | カスタムまたはレガシーAMM |
| **アップグレード** | Diamond Proxy (EIP-2535) | 再デプロイまたはアップグレード不可 |
| **オラクルシステム** | プラグ可能（Manual、Chainlink、拡張可能） | 単一オラクル、固定的 |
| **LP保護** | 動的手数料：満期近くで0.5% → 5% | 保護なし |

## アーキテクチャ概要

```
                         User
                          │
              ┌───────────┼───────────┐
              ▼           ▼           ▼
         PrediXRouter  PrediXExchange  Diamond Proxy
         (Smart Route)  (CLOB)        (Market Management)
              │           │           │
              │     ┌─────┘     ┌─────┘
              ▼     ▼           ▼
         Uniswap v4        MarketFacet ─── OutcomeTokens (ERC-20)
         PoolManager       AccessControl
              │            OwnableFacet
              ▼            PausableFacet
         PrediXHookV1
         (Dynamic Fees,       Oracle Adapters
          Anti-Sandwich)      ├── ManualOracle
                              ├── ChainlinkAdapter
                              └── [Future: UMA, API3]
```

### コアコントラクト

| コントラクト | 役割 |
| -------- | ---- |
| **Diamond** | マーケット管理：作成、分割、統合、解決、受取 |
| **Exchange** | 99-tickビットマップマッチングエンジンを備えたオンチェーンCLOB |
| **Router** | 最適な約定のためのCLOB + AMMスマートルーティング |
| **Hook** | Uniswap v4 Hook：動的手数料、サンドイッチ対策、プール登録 |
| **Oracle** | プラグ可能な解決システム（Manual、Chainlink、拡張可能） |

---

**次へ**: [仕組み](how-it-works.md) · [クイックスタート](quick-start.md) · [コアコンセプト](../concepts/prediction-markets.md)
