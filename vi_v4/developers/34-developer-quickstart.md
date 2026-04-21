---
description: Bắt đầu phát triển trên PrediX testnet trong 5 phút — connect, read market, place trade.
---

# Developer Quickstart

Mục tiêu: từ zero đến "bought YES tokens" trong 5 phút.

## Prerequisites

- Node.js ≥ 18
- `viem` ≥ 2.0 (recommended) hoặc `ethers` v6
- Ví hỗ trợ Unichain Sepolia — MetaMask, Rabby, WalletConnect
- ~0.01 UNI (testnet gas) + USDC testnet (faucet)

## Step 1 — Lấy testnet fund

Xem [Testnet Faucet & Setup](40-testnet-faucet.md) cho hướng dẫn chi tiết. Quick version:

1. Add Unichain Sepolia vào ví: Chain ID `1301`, RPC `https://sepolia.unichain.org`
2. Nhận 0.01 UNI từ [Unichain public faucet](https://faucet.unichain.org)
3. Nhận USDC testnet từ PrediX faucet UI tại `app-testnet.predixpro.io/faucet`

## Step 2 — Setup project

```bash
mkdir predix-demo && cd predix-demo
pnpm init -y
pnpm add viem
```

Lấy ABI: xem [ABIs & Typed Clients](39-abi-types.md). Quick copy:

```bash
mkdir abis
curl -o abis/MarketFacet.json https://raw.githubusercontent.com/predix-protocol/indexer/main/abis/MarketFacetAbi.json
curl -o abis/Router.json https://raw.githubusercontent.com/predix-protocol/indexer/main/abis/PrediXRouterAbi.json
curl -o abis/Exchange.json https://raw.githubusercontent.com/predix-protocol/indexer/main/abis/PrediXExchangeAbi.json
curl -o abis/ERC20.json https://raw.githubusercontent.com/OpenZeppelin/openzeppelin-contracts/master/contracts/token/ERC20/IERC20.sol
```

## Step 3 — Connect + read market

```typescript
// demo.ts
import { createPublicClient, createWalletClient, http, formatUnits } from 'viem';
import { privateKeyToAccount } from 'viem/accounts';
import { defineChain } from 'viem';
import MarketFacetAbi from './abis/MarketFacet.json' with { type: 'json' };

const unichainSepolia = defineChain({
  id: 1301,
  name: 'Unichain Sepolia',
  nativeCurrency: { name: 'Uni', symbol: 'UNI', decimals: 18 },
  rpcUrls: { default: { http: ['https://sepolia.unichain.org'] } },
  blockExplorers: { default: { name: 'Uniscan', url: 'https://sepolia.uniscan.xyz' } },
});

const DIAMOND = '0xF38a265E6e4F57D000a1CC08004da5B4A380B08A';
const ROUTER = '0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7';
const USDC = '0x12fd156C8b5F2901BA2781d97db84AaC56b2b911';

const publicClient = createPublicClient({
  chain: unichainSepolia,
  transport: http(),
});

// Replace với marketId thật — get từ `GET /api/v2/markets`
const marketId = 42n;

const market = await publicClient.readContract({
  address: DIAMOND,
  abi: MarketFacetAbi,
  functionName: 'getMarket',
  args: [marketId],
});

console.log('Question:', market.question);
console.log('End time:', new Date(Number(market.endTime) * 1000).toISOString());
console.log('Total collateral:', formatUnits(market.totalCollateral, 6), 'USDC');
console.log('YES token:', market.yesToken);
console.log('Resolved:', market.isResolved);
```

## Step 4 — Buy YES tokens

```typescript
import RouterAbi from './abis/Router.json' with { type: 'json' };
import ERC20Abi from './abis/ERC20.json' with { type: 'json' };

const PRIVATE_KEY = process.env.PRIVATE_KEY as `0x${string}`;
const account = privateKeyToAccount(PRIVATE_KEY);

const walletClient = createWalletClient({
  account,
  chain: unichainSepolia,
  transport: http(),
});

// 1. Approve USDC cho Router (one-time per token)
const usdcIn = 10_000_000n;  // 10 USDC (6 decimals)

const approveTx = await walletClient.writeContract({
  address: USDC,
  abi: ERC20Abi,
  functionName: 'approve',
  args: [ROUTER, usdcIn],
});
await publicClient.waitForTransactionReceipt({ hash: approveTx });

// 2. Get quote (revert-and-decode pattern)
let expectedYes: bigint;
try {
  await publicClient.simulateContract({
    address: ROUTER,
    abi: RouterAbi,
    functionName: 'quoteBuyYes',
    args: [marketId, usdcIn],
  });
} catch (err: any) {
  // Router revert with encoded quote
  expectedYes = decodeQuoteFromRevert(err.data);
}

// 3. Place trade with 2% slippage
const minYesOut = (expectedYes * 98n) / 100n;
const deadline = BigInt(Math.floor(Date.now() / 1000) + 300);  // 5 phút

const tx = await walletClient.writeContract({
  address: ROUTER,
  abi: RouterAbi,
  functionName: 'buyYes',
  args: [marketId, usdcIn, minYesOut, account.address, deadline],
});

const receipt = await publicClient.waitForTransactionReceipt({ hash: tx });
console.log('Trade tx:', receipt.transactionHash);
console.log('Gas used:', receipt.gasUsed);
```

Chạy:

```bash
PRIVATE_KEY=0x... npx tsx demo.ts
```

{% hint style="info" %}
**`decodeQuoteFromRevert`** là helper tự viết. Router v4-quoter pattern revert với encoded data. Sau Step 4 working, xem [Trading Integration](35-trading-integration.md) cho proper decoder.
{% endhint %}

## Step 5 — Verify ownership

```typescript
const yesBalance = await publicClient.readContract({
  address: market.yesToken,
  abi: ERC20Abi,
  functionName: 'balanceOf',
  args: [account.address],
});

console.log('Your YES balance:', formatUnits(yesBalance, 6));
```

## Full contract addresses

| Contract | Address |
|---|---|
| Diamond | `0xF38a265E6e4F57D000a1CC08004da5B4A380B08A` |
| Hook V2 (proxy) | `0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0` |
| Exchange | `0xa202abCb2A358c0862B2dA76b553398339F2C638` |
| Router | `0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7` |
| ManualOracle | `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3` |
| USDC (testnet) | `0x12fd156C8b5F2901BA2781d97db84AaC56b2b911` |
| PoolManager (Uniswap v4) | `0x00B036B58a818B1BC34d502D3fE730Db729e62AC` |
| Permit2 | `0x000000000022D473030F116dDEE9F6B43aC78BA3` |

Canonical: [Deployed Addresses](../smart-contracts/deployed-addresses.md).

## Next steps

- [ABIs & Typed Clients](39-abi-types.md) — fully typed setup
- [Trading Integration](35-trading-integration.md) — market + limit orders
- [Split, Merge, Resolve & Redeem](36-split-merge-resolve-redeem.md) — lifecycle operations
- [Event Listening](41-event-listening.md) — subscribe on-chain events
- [Error Handling](38-common-errors.md) — parse custom errors
- [Integration Patterns](42-integration-patterns.md) — per-persona guides

## Troubleshooting

| Issue | Fix |
|---|---|
| `Error: insufficient funds` | Lấy thêm UNI faucet cho gas |
| `Router_SlippageExceeded` | Tăng slippage tolerance (vd 5%) hoặc retry với quote mới |
| `Router_DeadlineExpired` | Deadline quá sát — dùng 5 phút thay vì 30s |
| `Market_NotFound` | marketId sai — check via `GET /api/v2/markets` |
| `Market_Ended` | Market đã qua endTime — chọn market active |
| `allowance` related | Chưa approve USDC → approve với `MaxUint256` cho one-time |
