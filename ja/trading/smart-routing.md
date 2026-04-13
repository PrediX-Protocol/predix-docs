---
description: RouterがCLOBとAMMの流動性を集約する方法
---

# スマートルーティング

PrediXRouterはCLOBとAMMの両方から自動的に最適な実行パスを見つけます。

## 仕組み

```
router.buyYes($100):
  ステップ1：CLOBでAMMより有利な価格のSELL_YES注文を確認
  ステップ2：CLOB注文を先に約定（最良価格、スリッページなし）
  ステップ3：残りの数量をUniswap v4 AMMにルーティング
  ステップ4：ユーザーが両方のソースからYESトークンの合計を受領
```

### 例

```
100 YESトークンの購入：
  CLOB：60 YES利用可能 @ $0.62 → コスト：$37.20
  AMM：40 YES @ 平均〜$0.65   → コスト：$26.00
  合計：100 YESを$63.20で購入

Smart Routerなし（AMMのみ）：
  100 YES @ 平均〜$0.66 → コスト：$66.00
  
  節約額：$2.80（4.2%の約定改善）
```

## 見積もり関数

取引前に必ず見積もりを取得してください：

```typescript
const router = new ethers.Contract(ROUTER_ADDRESS, ROUTER_ABI, provider);

const yesOut = await router.quoteBuyYes(marketId, usdcIn);
const usdcOut = await router.quoteSellYes(marketId, yesIn);
const noOut = await router.quoteBuyNo(marketId, usdcIn);
const usdcFromNo = await router.quoteSellNo(marketId, noIn);
```

## CLOBがAMMより有利な場合

| 条件 | より良い取引所 |
| ---- | -------------- |
| 深いオーダーブック、狭いスプレッド | CLOB |
| 希望価格に注文なし | AMM |
| 大口注文 | 両方に分散 |
| マーケット満了間近（高いAMM手数料） | CLOB |

---

**次へ**: [マッチングエンジン](matching-engine.md) · [仮想NO価格設定](virtual-no-pricing.md)
