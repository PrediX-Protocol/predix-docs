---
description: 5分でPrediXの開発を始めましょう
---

# 開発者クイックスタート

## 前提条件

- Node.js 18+
- ethers.js v6

## セットアップ

```bash
npm install ethers
```

## Unichain Sepolia に接続

```typescript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider("https://sepolia.unichain.org");
const signer = new ethers.Wallet(process.env.PRIVATE_KEY!, provider);

// コントラクトインスタンス
const DIAMOND = "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A";
const EXCHANGE = "0xa202abCb2A358c0862B2dA76b553398339F2C638";
const ROUTER = "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

const diamond = new ethers.Contract(DIAMOND, DIAMOND_ABI, signer);
const router = new ethers.Contract(ROUTER, ROUTER_ABI, signer);
const usdc = new ethers.Contract(USDC, ERC20_ABI, signer);
```

## マーケットの読み取り

```typescript
const marketId = "0x..."; // MarketCreated イベントまたはAPIから取得
const market = await diamond.getMarket(marketId);

console.log("質問:", market.question);
console.log("終了時刻:", new Date(Number(market.endTime) * 1000));
console.log("YES トークン:", market.yesToken);
console.log("NO トークン:", market.noToken);
console.log("解決済み:", market.isResolved);
console.log("担保金:", ethers.formatUnits(market.totalCollateral, 6), "USDC");
```

## 最初の取引を実行

```typescript
const usdcAmount = ethers.parseUnits("10", 6); // 10 USDC
const deadline = Math.floor(Date.now() / 1000) + 300; // 5分

// ステップ1: 見積もりを取得
const expectedYes = await router.quoteBuyYes(marketId, usdcAmount);
const minYesOut = expectedYes * 95n / 100n; // 5% スリッページ

// ステップ2: USDCを承認
await (await usdc.approve(ROUTER, usdcAmount)).wait();

// ステップ3: YESトークンを購入
const tx = await router.buyYes(marketId, usdcAmount, minYesOut, signer.address, deadline);
const receipt = await tx.wait();

console.log("取引ハッシュ:", receipt.hash);
```

---

**次へ**: [コントラクトアドレス](addresses.md) · [ABI](abis.md) · [取引統合](trading-integration.md)
