# Router integration

The Router is the single entry point for all swaps. Let your users trade through your app by calling the Router contract.

## ABI

Router address:
- **Testnet** (Unichain Sepolia, live): `0x6698253F38F4A4bbBC4A223309B4E560d83D7ee0`
- **Mainnet** (TBA, after launch)

Full address list: [Contract addresses](architecture.md#contract-addresses).

Core function:

```solidity
function buyYes(
    bytes32 marketId,
    uint256 usdcIn,
    uint256 minYesOut,
    address recipient,
    uint256 maxFills,
    uint256 deadline
) external returns (
    uint256 yesOut,
    uint256 clobFilled,
    uint256 ammFilled
);

function sellYes(
    bytes32 marketId,
    uint256 yesIn,
    uint256 minUsdcOut,
    address recipient,
    uint256 maxFills,
    uint256 deadline
) external returns (
    uint256 usdcOut,
    uint256 clobFilled,
    uint256 ammFilled
);

// buyNo, sellNo are symmetric
```

## Quote before swap

```solidity
function quoteBuyYesExactIn(
    bytes32 marketId,
    uint256 usdcIn
) external view returns (
    uint256 expectedYesOut,
    uint256 priceImpactBps
);
```

View function, free to call. Use it to show a preview to users before they sign.

## Viem / TypeScript example

```typescript
import { createPublicClient, createWalletClient, custom, http, parseUnits } from 'viem';
import { unichainSepolia } from 'viem/chains';  // current testnet
// import { unichain } from 'viem/chains';      // mainnet after launch
import { routerAbi } from '@predix/abi';

const publicClient = createPublicClient({
  chain: unichainSepolia,
  transport: http('https://sepolia.unichain.org'),
});

const walletClient = createWalletClient({
  chain: unichainSepolia,
  transport: custom(window.ethereum),
});

// Testnet Router (mainnet TBA)
const ROUTER = '0x6698253F38F4A4bbBC4A223309B4E560d83D7ee0';
const marketId = '0x000000...0001'; // 32-byte hex

// 1. Quote
const [expectedOut, priceImpactBps] = await publicClient.readContract({
  address: ROUTER,
  abi: routerAbi,
  functionName: 'quoteBuyYesExactIn',
  args: [marketId, parseUnits('100', 6)], // 100 USDC
});

console.log(`Expected: ${expectedOut} YES, impact: ${priceImpactBps/100}%`);

// 2. Compute minOut with 0.5% slippage
const minOut = (expectedOut * 995n) / 1000n;

// 3. Execute (user signs tx)
const [account] = await walletClient.getAddresses();
const hash = await walletClient.writeContract({
  address: ROUTER,
  abi: routerAbi,
  functionName: 'buyYes',
  args: [
    marketId,
    parseUnits('100', 6),     // usdcIn
    minOut,                    // minYesOut
    account,                   // recipient
    10n,                       // maxFills (CLOB depth)
    BigInt(Math.floor(Date.now()/1000) + 300), // deadline 5min
  ],
  account,
});

const receipt = await publicClient.waitForTransactionReceipt({ hash });
console.log('Trade complete:', receipt.transactionHash);
```

## Approve USDC

The Router needs to pull USDC from the user via **Permit2** (gasless approve).

### Option 1: ERC-20 approve + Permit2 pull

```typescript
// 1. User approves USDC for Permit2 (one-time per token)
await walletClient.writeContract({
  address: USDC,
  abi: erc20Abi,
  functionName: 'approve',
  args: [PERMIT2, MAX_UINT256],
});

// 2. Permit2 sign off-chain, included in Router call
// See @uniswap/permit2-sdk docs for detailed encoding
```

### Option 2: Direct approve to Router (less recommended — not gasless)

```typescript
await walletClient.writeContract({
  address: USDC,
  abi: erc20Abi,
  functionName: 'approve',
  args: [ROUTER, MAX_UINT256],
});
```

## Permit2 signed approve (gas reduction)

Instead of 2 txs (approve + swap), the user signs a Permit2 message offline and includes the signature in a single swap tx:

```typescript
import { SignatureTransfer } from '@uniswap/permit2-sdk';

const permit = SignatureTransfer.createPermit(
  USDC, amount, spender: ROUTER, deadline
);
const signature = await walletClient.signTypedData(permit);

await walletClient.writeContract({
  address: ROUTER,
  abi: routerAbi,
  functionName: 'buyYesWithPermit',
  args: [marketId, usdcIn, minOut, recipient, permit, signature, ...],
});
```

Permit2 details: [docs.uniswap.org/contracts/permit2](https://docs.uniswap.org/contracts/permit2/overview).

## Encode marketId

Market ID is a **bytes32 hex** (64 chars after `0x`). The Indexer and FE use **decimal strings** for URLs.

```typescript
// Decimal -> hex (BE wire format -> contract input)
const marketIdHex = '0x' + BigInt(decimalString).toString(16).padStart(64, '0');

// Reverse
const decimal = BigInt(marketIdHex).toString();
```

## Handle errors

The Router reverts with custom errors. Decode using the 4-byte selector:

```solidity
error SlippageExceeded(uint256 actual, uint256 min);
error DeadlineExpired();
error MarketPaused();
error MarketNotActive();
error InsufficientLiquidity(uint256 requested, uint256 available);
error FinalizeBalanceNonZero(); // internal bug, report if encountered
```

In viem:

```typescript
try {
  await walletClient.writeContract({ ... });
} catch (err: any) {
  if (err.cause?.data?.errorName === 'SlippageExceeded') {
    // handle slippage
  }
}
```

## Emitted events

After a swap, the Router emits:

```solidity
event Trade(
    address indexed trader,
    address indexed recipient,
    bytes32 indexed marketId,
    uint8 tradeType,        // 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
    uint256 amountIn,
    uint256 amountOut,
    uint256 yesPrice,       // 6 decimals
    uint256 clobFilled,
    uint256 ammFilled
);
```

This event is the **canonical source** for the indexer. Listen to it to update your UI after tx confirmation.

## Batch with Smart Account

![AA UserOp batch](../_design/22-aa-userop.svg)

```typescript
import { createKernelClient } from '@zerodev/sdk';

const kernelClient = createKernelClient({...});

const calls = [
  {
    to: USDC,
    data: encodeFunctionData({
      abi: erc20Abi, functionName: 'approve', args: [ROUTER, MAX]
    }),
  },
  {
    to: ROUTER,
    data: encodeFunctionData({
      abi: routerAbi, functionName: 'buyYes', args: [...]
    }),
  },
];

const userOpHash = await kernelClient.sendUserOperation({
  callData: await kernelClient.encodeCalls(calls),
});

const txHash = await kernelClient.waitForUserOperationReceipt({
  hash: userOpHash,
});
```

Gas is paymaster-sponsored if the user has an active SIWE session via the PrediX app.

## AMM-only or CLOB-only

The Router always uses hybrid routing. If you want:
- **AMM-only** (skip CLOB): call Uniswap v4's UniversalRouter directly.
- **CLOB-only** (skip AMM): call `PrediXExchange.fillMarketOrder` or `placeOrder`.

The default recommendation is to use `PrediXRouter` — it optimizes price across both sources.

## Test on local fork

```bash
# Anvil fork Unichain Sepolia (current testnet)
anvil --fork-url https://sepolia.unichain.org

# Or fork mainnet after launch
# anvil --fork-url https://mainnet.unichain.org

# Deploy test contract or call directly
forge script test/Integration.s.sol --fork-url http://localhost:8545
```

Full examples: [github.com/predix-protocol/integration-examples](https://github.com/predix-protocol/integration-examples).

## Common patterns

### Auto-slippage

```typescript
async function smartSlippage(usdcIn: bigint): Promise<bigint> {
  // Try 0.5% first
  let slippage = 50n; // bps

  while (slippage <= 500n) {
    try {
      const [expected] = await quoteBuyYes(marketId, usdcIn);
      const minOut = (expected * (10000n - slippage)) / 10000n;
      return minOut;
    } catch (err) {
      slippage += 50n;
    }
  }
  throw new Error('Liquidity too thin');
}
```

### Multi-market batch

```typescript
const trades = [
  { marketId: 'm1', usdcIn: 100n },
  { marketId: 'm2', usdcIn: 50n },
];

const calls = trades.map(t => ({
  to: ROUTER,
  data: encodeFunctionData({
    abi: routerAbi,
    functionName: 'buyYes',
    args: [t.marketId, t.usdcIn, ...],
  }),
}));

await kernelClient.sendUserOperation({
  callData: await kernelClient.encodeCalls(calls),
});
```

1 UserOp executes N trades atomically.
