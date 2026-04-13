---
description: 数分でPrediXでの取引または開発を開始
---

# クイックスタート

## トレーダー向け

### ステップ1：ウォレット接続

Unichain Sepoliaをウォレットに追加：

| パラメータ | 値 |
| --------- | ----- |
| Network Name | Unichain Sepolia |
| RPC URL | `https://sepolia.unichain.org` |
| Chain ID | `1301` |
| Currency Symbol | ETH |
| Block Explorer | `https://sepolia.uniscan.xyz` |

### ステップ2：テストUSDCの取得

PrediX faucetからテストネットUSDCを取得、またはテストネットで直接ミント。

### ステップ3：USDCの承認

RouterコントラクトがUSDCを使用できるよう承認：

```
USDC Address: 0x12fd156C8b5F2901BA2781d97db84AaC56b2b911
Router Address: 0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7
```

### ステップ4：取引

- **YES購入**: `router.buyYes(marketId, usdcAmount, minYesOut, yourAddress, deadline)`を呼び出し
- **NO購入**: `router.buyNo(marketId, usdcAmount, mintAmount, minNoOut, yourAddress, deadline)`を呼び出し

### ステップ5：解決後の受取

マーケットが解決されたら、`diamond.redeemMarketTokens(marketId)`を呼び出して賞金を受け取ります。

---

## 開発者向け

### コントラクトアドレス（Unichain Sepolia）

| コントラクト | アドレス |
| -------- | ------- |
| Diamond | `0xF38a265E6e4F57D000a1CC08004da5B4A380B08A` |
| Hook | `0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0` |
| Exchange | `0xa202abCb2A358c0862B2dA76b553398339F2C638` |
| Router | `0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7` |
| Oracle | `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3` |
| USDC | `0x12fd156C8b5F2901BA2781d97db84AaC56b2b911` |
| Pool Manager | `0x00B036B58a818B1BC34d502D3fE730Db729e62AC` |

### クイックコード例

```typescript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider("https://sepolia.unichain.org");
const signer = new ethers.Wallet(PRIVATE_KEY, provider);

const diamond = new ethers.Contract(
  "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A",
  DIAMOND_ABI,
  signer
);

// マーケットを読み取り
const market = await diamond.getMarket(marketId);
console.log("Question:", market.question);
console.log("YES token:", market.yesToken);
console.log("Total collateral:", ethers.formatUnits(market.totalCollateral, 6));
```

---

**次へ**: [開発者クイックスタート](../developers/quickstart.md) · [コントラクトアドレス](../developers/addresses.md) · [取引統合](../developers/trading-integration.md)
