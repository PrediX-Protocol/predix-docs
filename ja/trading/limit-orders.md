---
description: オンチェーンCLOBでの指値注文の配置と管理
---

# 指値注文

指値注文はPrediXExchange（CLOB）に直接配置されます。約定、キャンセル、またはマーケット満了まで、オンチェーンに残ります。

## 注文配置

```typescript
const exchange = new ethers.Contract(
  "0xa202abCb2A358c0862B2dA76b553398339F2C638",
  EXCHANGE_ABI,
  signer
);

// Side列挙型：0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
const side = 0; // BUY_YES
const price = ethers.parseUnits("0.65", 6);   // トークンあたり$0.65
const amount = ethers.parseUnits("100", 6);    // 100トークン

// 買い注文の場合：USDCをExchangeに承認
// deposit = price × amount / 1e6
await usdc.approve(exchange.target, price * amount / 1000000n);

const tx = await exchange.placeOrder(marketId, side, price, amount);
const receipt = await tx.wait();
// OrderPlacedイベントからorderIdを解析
```

## 注文キャンセル

```typescript
await exchange.cancelOrder(orderId);
// 未約定の預入金が注文所有者に返還
```

> ⚠️ 満了したマーケットの注文は、誰でもキャンセルできます（endTime後）。

## 制約条件

| パラメータ | 値 |
| ---------- | -- |
| 価格範囲 | $0.01〜$0.99（6小数点で`10000`〜`990000`） |
| 価格ステップ | $0.01（`10000`） |
| 最小注文数量 | $1.00（6小数点で`1000000`） |
| ユーザーあたりマーケットあたりの最大注文数 | 50 |
| 配置あたりの最大約定数 | 20 |
| 自己取引防止 | maker.owner ≠ taker.owner |

## Side列挙型

```solidity
enum Side {
    BUY_YES,   // 0
    SELL_YES,  // 1
    BUY_NO,    // 2
    SELL_NO    // 3
}
```

---

**次へ**: [マッチングエンジン](matching-engine.md) · [スマートルーティング](smart-routing.md) · [オーダーブック](order-book.md)
