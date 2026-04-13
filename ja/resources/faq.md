---
description: PrediX Protocolについてのよくある質問
---

# FAQ

## PrediXとは何ですか？

PrediX Protocolは、Uniswap v4（Unichain L2）上に構築された分散型予測市場プラットフォームです。オンチェーンCLOBとAMMを組み合わせて、最適な取引体験を提供します。

## 取引を始めるには？

1. Unichain Sepoliaにウォレットを接続
2. Faucetからテスト用USDCを取得
3. RouterコントラクトにUSDCを承認
4. Routerで`buyYes`または`buyNo`を呼び出し

詳細は[クイックスタート](../getting-started/quick-start.md)ガイドを参照してください。

## マーケットが解決されるとどうなりますか？

オラクルが結果を報告した後、誰でも`resolveMarket`を呼び出せます。勝利トークン保有者はトークンあたり$1.00を受け取ります。敗北トークンは無価値になります。

## 投資額以上を失うことはありますか？

**いいえ。** 最大損失はアウトカムトークンに支払った金額です。レバレッジ、マージン、清算はありません。

## 価格はどのように決まりますか？

価格はAMMプールとCLOBオーダーブックの需給によって設定されます。Smart Routerが両方のソースを集約します。価格は自然にマーケットの集合的な確率推定を反映します。

## Split/Mergeとは？

- **Split**: 1 USDCをデポジット → 1 YES + 1 NOトークンを受取
- **Merge**: 1 YES + 1 NOをバーン → 1 USDCを受取

このメカニズムがYES + NO ≈ $1.00でトークン価格をアンカーします。

## 手数料はいくらですか？

AMM手数料は0.5%（満期まで7日以上）から5%（24時間未満）の範囲です。CLOB手数料はMINT/MERGE剰余金から発生します。[手数料](../concepts/fees.md)を参照してください。

## PrediXは監査済みですか？

はい。264テスト、14ファズテスト（各10K実行）、31オンチェーンE2Eテスト、3件の独立監査で22件のバグを発見・修正、OWASP SC Top 10 2026で10/10のカバレッジ。[監査レポート](../security/audit-reports.md)を参照。

## PrediXはどのチェーンですか？

Unichain Sepolia（テストネット）。Unichainメインネットへのデプロイは2026年Q3を予定しています。

## テスト用USDCはどうやって入手しますか？

PrediXテストネットfaucetを訪問するか、Unichain Sepoliaのアドレス`0x12fd156C8b5F2901BA2781d97db84AaC56b2b911`で直接ミントしてください。

---

**次へ**: [用語集](glossary.md) · [クイックスタート](../getting-started/quick-start.md)
