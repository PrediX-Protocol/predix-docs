---
description: スマートコントラクトのアーキテクチャとデプロイアドレス
---

# コントラクト概要

## アーキテクチャ

PrediX Protocolは5つのコアコントラクトで構成されています：

| コントラクト | アドレス (Unichain Sepolia) | 役割 |
| -------- | -------------------------- | ---- |
| **Diamond** | `0xF38a265E6e4F57D000a1CC08004da5B4A380B08A` | マーケット管理 (EIP-2535 プロキシ) |
| **Hook** | `0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0` | Uniswap v4 Hook：動的手数料 + サンドイッチ攻撃防止 |
| **Exchange** | `0xa202abCb2A358c0862B2dA76b553398339F2C638` | オンチェーン CLOB オーダーブック |
| **Router** | `0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7` | スマートルーティング：CLOB + AMM 統合 |
| **Oracle** | `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3` | ManualOracleAdapter |

### 外部依存関係

| コントラクト | アドレス |
| -------- | ------- |
| USDC | `0x12fd156C8b5F2901BA2781d97db84AaC56b2b911` |
| Uniswap v4 PoolManager | `0x00B036B58a818B1BC34d502D3fE730Db729e62AC` |

## Diamond Proxy (EIP-2535) を採用する理由

- **24KB制限の回避**：複雑なプロトコルロジックがEVMコントラクトサイズ制限を超過
- **細粒度のアップグレード**：全体の再デプロイなしに個別の関数を置換可能
- **共有ストレージ**：すべてのFacetが同じストレージレイアウトを共有
- **本番環境で実績あり**：Aavegotchi、Beanstalkなどで使用

## チェーン情報

| パラメータ | 値 |
| --------- | ----- |
| ネットワーク | Unichain Sepolia (テストネット) |
| チェーンID | `1301` |
| RPC | `https://sepolia.unichain.org` |
| エクスプローラー | `https://sepolia.uniscan.xyz` |

---

**次へ**: [Diamond コントラクト](diamond.md) · [Exchange](exchange.md) · [Router](router.md) · [Hook](hook.md) · [Oracle](oracle.md)
