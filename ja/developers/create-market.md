---
description: プログラムで予測マーケットを作成する
---

# マーケット作成

> ⚠️ マーケット作成には**ADMIN_ROLE**が必要です。

## ステップバイステップ

```typescript
import { ethers } from "ethers";

const diamond = new ethers.Contract(DIAMOND_ADDRESS, DIAMOND_ABI, signer);

// パラメータ
const question = "Will BTC exceed $100,000 by December 31, 2026?";
const description = "Resolves YES if Bitcoin price (Chainlink BTC/USD) >= $100,000.";
const endTime = Math.floor(new Date("2026-12-31T23:59:59Z").getTime() / 1000);
const categoryId = ethers.ZeroHash; // カテゴリなし（スタンドアロンマーケット）
const oracle = "0x699A8C74663b1C852E195b2ffa00D5965E992Cf3"; // ManualOracle

// 作成
const tx = await diamond.createMarket(question, description, endTime, categoryId, oracle);
const receipt = await tx.wait();

// MarketCreated イベントをパース
const event = receipt.logs.find(
  (log) => diamond.interface.parseLog(log)?.name === "MarketCreated"
);
const parsed = diamond.interface.parseLog(event);
const marketId = parsed.args.marketId;

console.log("マーケット ID:", marketId);
console.log("YES トークン:", parsed.args.yesToken);
console.log("NO トークン:", parsed.args.noToken);
```

## 自動的に実行される処理

`createMarket`が呼び出されると:

1. YES および NO ERC-20 トークンがデプロイされます（CREATE2）
2. Uniswap v4 AMM プール（YES/USDC）が作成され、$0.50で初期化されます
3. プールが Hook に登録されます
4. マーケットデータが Diamond ストレージに保存されます

## パラメータ

| パラメータ | 型 | 説明 |
| --------- | ---- | ----------- |
| `question` | string | マーケットの質問 |
| `description` | string | 追加情報 |
| `endTime` | uint256 | 取引が停止するUnixタイムスタンプ |
| `categoryId` | bytes32 | カテゴリID（スタンドアロンの場合は`bytes32(0)`） |
| `oracle` | address | 承認されたオラクルアダプターアドレス |

## オンチェーン検証

```typescript
const market = await diamond.getMarket(marketId);
console.log("存在:", await diamond.isMarketExists(marketId));
console.log("オラクル:", market.oracle);
console.log("終了時刻:", new Date(Number(market.endTime) * 1000));
```

---

**次へ**: [取引統合](trading-integration.md) · [分割 & マージ](split-merge.md)
