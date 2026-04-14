# Quick Start

## For Traders

### 5 Steps to Your First Trade

1. **Connect wallet** to Unichain Sepolia (Chain ID: `1301`, RPC: `https://sepolia.unichain.org`)
2. **Get test USDC** from the [Unichain Sepolia Faucet](https://faucet.unichain.org) or mint via the test USDC contract
3. **Approve USDC** to the Router contract
4. **Choose a market** and decide YES or NO
5. **Execute trade** via the Router — `buyYes()` or `buyNo()`

> ⚠️ This is testnet. Tokens have no real value. Use only test USDC.

### Trade via dApp

Visit the PrediX web app, connect your wallet, select a market, and trade directly from the UI.

### Trade via Contract

```typescript
import { ethers } from "ethers";

const ROUTER = "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

// 1. Approve USDC
const usdc = new ethers.Contract(USDC, ["function approve(address,uint256)"], signer);
await usdc.approve(ROUTER, ethers.parseUnits("100", 6));

// 2. Buy YES tokens
const router = new ethers.Contract(ROUTER, [
  "function buyYes(bytes32,uint256,uint256,address,uint256) returns (uint256)"
], signer);

const tx = await router.buyYes(
  marketId,                          // bytes32
  ethers.parseUnits("100", 6),       // 100 USDC in
  ethers.parseUnits("90", 6),        // min 90 YES out (slippage)
  await signer.getAddress(),          // recipient
  Math.floor(Date.now() / 1000) + 300 // 5 min deadline
);
```

## For Developers

- **Contract addresses & ABIs:** [Contracts Overview](../contracts/overview.md)
- **Full integration guide:** [Developer Quickstart](../developers/quickstart.md)
- **Code examples:** [Trading Integration](../developers/trading-integration.md)

## Next Steps

- [Trading Overview](../trading/overview.md) — market orders, limit orders, routing
- [Prediction Markets 101](../concepts/prediction-markets.md) — how prices work
