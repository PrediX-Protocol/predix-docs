---
description: Get started trading or building on PrediX in minutes
---

# Quick Start

## For Traders

### Step 1: Connect Wallet

Add Unichain Sepolia to your wallet:

| Parameter | Value |
| --------- | ----- |
| Network Name | Unichain Sepolia |
| RPC URL | `https://sepolia.unichain.org` |
| Chain ID | `1301` |
| Currency Symbol | ETH |
| Block Explorer | `https://sepolia.uniscan.xyz` |

### Step 2: Get Test USDC

Get testnet USDC from the PrediX faucet or mint directly on testnet.

### Step 3: Approve USDC

Approve the Router contract to spend your USDC:

```
USDC Address: 0x12fd156C8b5F2901BA2781d97db84AaC56b2b911
Router Address: 0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7
```

### Step 4: Trade

- **Buy YES**: Call `router.buyYes(marketId, usdcAmount, minYesOut, yourAddress, deadline)`
- **Buy NO**: Call `router.buyNo(marketId, usdcAmount, mintAmount, minNoOut, yourAddress, deadline)`

### Step 5: Redeem After Resolution

Once the market resolves, call `diamond.redeemMarketTokens(marketId)` to collect your winnings.

---

## For Developers

### Contract Addresses (Unichain Sepolia)

| Contract | Address |
| -------- | ------- |
| Diamond | `0xF38a265E6e4F57D000a1CC08004da5B4A380B08A` |
| Hook | `0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0` |
| Exchange | `0xa202abCb2A358c0862B2dA76b553398339F2C638` |
| Router | `0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7` |
| Oracle | `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3` |
| USDC | `0x12fd156C8b5F2901BA2781d97db84AaC56b2b911` |
| Pool Manager | `0x00B036B58a818B1BC34d502D3fE730Db729e62AC` |

### Quick Code Example

```typescript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider("https://sepolia.unichain.org");
const signer = new ethers.Wallet(PRIVATE_KEY, provider);

const diamond = new ethers.Contract(
  "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A",
  DIAMOND_ABI,
  signer
);

// Read a market
const market = await diamond.getMarket(marketId);
console.log("Question:", market.question);
console.log("YES token:", market.yesToken);
console.log("Total collateral:", ethers.formatUnits(market.totalCollateral, 6));
```

---

**Next**: [Developer Quickstart](../developers/quickstart.md) · [Contract Addresses](../developers/addresses.md) · [Trading Integration](../developers/trading-integration.md)
