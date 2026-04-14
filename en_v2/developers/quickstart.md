# Developer Quickstart

Build on PrediX in under 10 minutes. This guide covers connecting, reading market data, and placing your first trade.

## Prerequisites

- Node.js 18+
- ethers.js v6 (`npm install ethers`)
- TypeScript (recommended)

## Connect to Unichain Sepolia

```typescript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider("https://sepolia.unichain.org");
const signer = new ethers.Wallet("YOUR_PRIVATE_KEY", provider);

// Verify connection
const chainId = (await provider.getNetwork()).chainId;
console.log(`Connected to chain ${chainId}`); // 1301
```

For contract addresses and ABIs, see [Contracts Overview](../contracts/overview.md).

## Read a Market

```typescript
const DIAMOND = "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A";

const diamond = new ethers.Contract(DIAMOND, [
  "function getMarket(bytes32) view returns (tuple(string question, string description, uint256 endTime, address yesToken, address noToken, bytes32 poolId, bytes32 categoryId, address oracle, bool isResolved, bool outcome, uint256 totalCollateral, bytes32 questionHash, uint256 resolvedAt))"
], provider);

const market = await diamond.getMarket(marketId);
console.log(`Question: ${market.question}`);
console.log(`End time: ${new Date(Number(market.endTime) * 1000)}`);
console.log(`Resolved: ${market.isResolved}`);
console.log(`Collateral: ${ethers.formatUnits(market.totalCollateral, 6)} USDC`);
```

## Place Your First Trade

```typescript
const ROUTER = "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

const usdc = new ethers.Contract(USDC, [
  "function approve(address,uint256)",
  "function balanceOf(address) view returns (uint256)"
], signer);

const router = new ethers.Contract(ROUTER, [
  "function buyYes(bytes32,uint256,uint256,address,uint256) returns (uint256)",
  "function quoteBuyYes(bytes32,uint256) view returns (uint256)"
], signer);

// 1. Check balance
const balance = await usdc.balanceOf(signer.address);
console.log(`USDC balance: ${ethers.formatUnits(balance, 6)}`);

// 2. Get quote
const usdcIn = ethers.parseUnits("10", 6);
const expectedYes = await router.quoteBuyYes(marketId, usdcIn);
console.log(`Expected YES: ${ethers.formatUnits(expectedYes, 6)}`);

// 3. Approve USDC
await (await usdc.approve(ROUTER, usdcIn)).wait();

// 4. Buy YES with 5% slippage tolerance
const minOut = expectedYes * 95n / 100n;
const deadline = Math.floor(Date.now() / 1000) + 300;

const tx = await router.buyYes(marketId, usdcIn, minOut, signer.address, deadline);
const receipt = await tx.wait();
console.log(`Trade executed: ${receipt.hash}`);
```

## Get Test USDC

Visit the [Unichain Sepolia Faucet](https://faucet.unichain.org) or interact with the test USDC contract directly to mint tokens.

## Next Steps

- [ABIs](abis.md) — download ABI files
- [Create Market](create-market.md) — deploy your own market
- [Trading Integration](trading-integration.md) — full trading code guide
- [Events](events.md) — listen for on-chain events
