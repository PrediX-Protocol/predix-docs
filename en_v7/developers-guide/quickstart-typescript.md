# Quickstart — TypeScript

Buy YES tokens on a PrediX market in under 5 minutes using viem.

## Prerequisites

```bash
npm install viem
```

You need:
- A wallet private key with ETH on Unichain Sepolia (for gas)
- USDC on Unichain Sepolia (get from [Faucet](testnet.md))

## 1. Setup client

```typescript
import { createPublicClient, createWalletClient, http, parseUnits } from 'viem';
import { privateKeyToAccount } from 'viem/accounts';
import { unichainSepolia } from 'viem/chains';

const RPC = 'https://sepolia.unichain.org';

const publicClient = createPublicClient({
  chain: unichainSepolia,
  transport: http(RPC),
});

const account = privateKeyToAccount('0x...');  // your private key
const walletClient = createWalletClient({
  chain: unichainSepolia,
  transport: http(RPC),
  account,
});
```

## 2. Contract addresses

```typescript
const ROUTER = '0x1267723f500C0437295698d36d521bd060Bed0EB' as const;
const USDC   = '0x5a9153c368946B5b252c32921EbB3c16c692D7D4' as const;
const PERMIT2 = '0x000000000022D473030F116dDEE9F6B43aC78BA3' as const;
```

## 3. Approve USDC (one-time)

```typescript
import { erc20Abi } from 'viem';

// Approve USDC to Permit2 (one-time, max amount)
const approveTx = await walletClient.writeContract({
  address: USDC,
  abi: erc20Abi,
  functionName: 'approve',
  args: [PERMIT2, parseUnits('1000000', 6)],  // 1M USDC max
});
await publicClient.waitForTransactionReceipt({ hash: approveTx });
console.log('USDC approved to Permit2');
```

## 4. Quote before trading

```typescript
const routerAbi = [
  {
    name: 'quoteBuyYes',
    type: 'function',
    stateMutability: 'nonpayable',  // NOT view — use eth_call
    inputs: [
      { name: 'marketId', type: 'uint256' },
      { name: 'usdcIn', type: 'uint256' },
      { name: 'maxFills', type: 'uint256' },
    ],
    outputs: [
      { name: 'expectedYesOut', type: 'uint256' },
      { name: 'clobPortion', type: 'uint256' },
      { name: 'ammPortion', type: 'uint256' },
    ],
  },
] as const;

const marketId = 1n;  // market ID (uint256)
const usdcIn = parseUnits('100', 6);  // 100 USDC

// Quote is non-view — use simulate (eth_call)
const { result } = await publicClient.simulateContract({
  address: ROUTER,
  abi: routerAbi,
  functionName: 'quoteBuyYes',
  args: [marketId, usdcIn, 10n],  // maxFills = 10
  account,
});

const [expectedYesOut, clobPortion, ammPortion] = result;
console.log(`Expected: ${expectedYesOut} YES (CLOB: ${clobPortion}, AMM: ${ammPortion})`);
```

> **Important**: Quote functions are NOT `view` — they must be called via `eth_call` (simulateContract). They return `(0, 0, 0)` on invalid market state, never revert.

## 5. Execute trade

```typescript
const buyYesAbi = [
  {
    name: 'buyYes',
    type: 'function',
    stateMutability: 'nonpayable',
    inputs: [
      { name: 'marketId', type: 'uint256' },
      { name: 'usdcIn', type: 'uint256' },
      { name: 'minYesOut', type: 'uint256' },
      { name: 'recipient', type: 'address' },
      { name: 'maxFills', type: 'uint256' },
      { name: 'deadline', type: 'uint256' },
    ],
    outputs: [
      { name: 'yesOut', type: 'uint256' },
      { name: 'clobFilled', type: 'uint256' },
      { name: 'ammFilled', type: 'uint256' },
    ],
  },
] as const;

// 0.5% slippage
const minOut = (expectedYesOut * 995n) / 1000n;
const deadline = BigInt(Math.floor(Date.now() / 1000) + 300);  // 5 min

const hash = await walletClient.writeContract({
  address: ROUTER,
  abi: buyYesAbi,
  functionName: 'buyYes',
  args: [marketId, usdcIn, minOut, account.address, 10n, deadline],
});

const receipt = await publicClient.waitForTransactionReceipt({ hash });
console.log('Trade complete:', receipt.transactionHash);
```

## 6. Check position

```typescript
// Via Indexer API
const res = await fetch(
  `${INDEXER_URL}/api/portfolio/${account.address}`
);
const positions = await res.json();
console.log(positions);
```

## Router functions reference

| Function | Input | Output | Description |
|---|---|---|---|
| `buyYes` | marketId, usdcIn, minYesOut, recipient, maxFills, deadline | yesOut, clobFilled, ammFilled | Buy YES tokens with USDC |
| `sellYes` | marketId, yesIn, minUsdcOut, recipient, maxFills, deadline | usdcOut, clobFilled, ammFilled | Sell YES tokens for USDC |
| `buyNo` | marketId, usdcIn, minNoOut, recipient, maxFills, deadline | noOut, clobFilled, ammFilled | Buy NO tokens with USDC |
| `sellNo` | marketId, noIn, minUsdcOut, recipient, maxFills, deadline | usdcOut, clobFilled, ammFilled | Sell NO tokens for USDC |
| `quoteBuyYes` | marketId, usdcIn, maxFills | expectedOut, clob, amm | Quote (non-view, use eth_call) |
| `quoteSellYes` | marketId, yesIn, maxFills | expectedOut, clob, amm | Quote |
| `quoteBuyNo` | marketId, usdcIn, maxFills | expectedOut, clob, amm | Quote |
| `quoteSellNo` | marketId, noIn, maxFills | expectedOut, clob, amm | Quote |

Permit2 variants: `buyYesWithPermit`, `sellYesWithPermit`, `buyNoWithPermit`, `sellNoWithPermit` — same params + `permitSingle` + `signature`.

## Common errors

| Error | Cause | Fix |
|---|---|---|
| `SlippageExceeded` | Price moved past tolerance | Increase slippage or retry |
| `DeadlineExpired` | Tx took too long | Increase deadline |
| `MarketPaused` | Market paused by admin | Wait for resume |
| `MarketNotActive` | Market ended or resolved | Cannot trade |
| `InsufficientLiquidity` | Not enough depth | Reduce size |
| `FinalizeBalanceNonZero` | Internal error | Report as bug |

## Next steps

- [Router integration](router-integration.md) — Permit2 flow, batch with Smart Account, AMM-only / CLOB-only
- [API reference](api-reference.md) — REST endpoints for market data
- [Testnet info](testnet.md) — faucet, RPC endpoints
