# Router integration

The Router is the single entry point for all swaps. Let your users trade through your app by calling the Router contract.

## ABI

Router address:

* **Beta** (Unichain mainnet, live): `0xf7D11488B6B0DAc511aE637AB02876dbE36cAdD6`
* **Production** (TBA, after launch)

Full address list: [Contract addresses](../protocol/architecture.md#contract-addresses).

Core function:

```solidity
function buyYes(
    uint256 marketId,
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
    uint256 marketId,
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
function quoteBuyYes(
    uint256 marketId,
    uint256 usdcIn,
    uint256 maxFills
) external returns (
    uint256 expectedYesOut,
    uint256 clobPortion,
    uint256 ammPortion
);
```

Non-view function (modifies transient state). Use `simulateContract` (not `readContract`) to call it off-chain. Use it to show a preview to users before they sign.

## Viem / TypeScript example

```typescript
import { createPublicClient, createWalletClient, custom, http, parseUnits } from 'viem';
import { unichain } from 'viem/chains';  // current beta on mainnet
// Define Router ABI inline or import from source

const publicClient = createPublicClient({
  chain: unichain,
  transport: http('https://mainnet.unichain.org'),
});

const walletClient = createWalletClient({
  chain: unichain,
  transport: custom(window.ethereum),
});

// Beta Router (production TBA)
const ROUTER = '0xf7D11488B6B0DAc511aE637AB02876dbE36cAdD6';
const marketId = 1n; // uint256

// 1. Quote (non-view, use simulateContract)
const { result: [expectedOut, clobPortion, ammPortion] } = await publicClient.simulateContract({
  address: ROUTER,
  abi: routerAbi,
  functionName: 'quoteBuyYes',
  args: [marketId, parseUnits('100', 6), 10n], // 100 USDC, maxFills=10
});

console.log(`Expected: ${expectedOut} YES, CLOB: ${clobPortion}, AMM: ${ammPortion}`);

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

### Option 2: Direct approve to Router (less recommended - not gasless)

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
  args: [marketId, usdcIn, minOut, recipient, maxFills, deadline, permit, signature],
});
```

The Router requires **exact-amount** permits: `permitSingle.details.amount` must equal `usdcIn`, and `permitSingle.spender` must equal the Router address. A mismatch reverts with `InvalidPermitAmount` / `InvalidPermitSpender`. Sign a fresh per-trade permit for each call.

Permit2 details: [docs.uniswap.org/contracts/permit2](https://docs.uniswap.org/contracts/permit2/overview).

## Encode marketId

Market ID is a **uint256**. The Indexer, BE, and FE use **decimal strings** for URLs and wire format.

```typescript
// Decimal string -> BigInt (for contract calls)
const marketId = BigInt(decimalString);

// Reverse
const decimal = marketId.toString();
```

## Handle errors

The Router reverts with custom errors. Decode using the 4-byte selector:

```solidity
error InsufficientOutput(uint256 actual, uint256 minimum);
error DeadlineExpired(uint256 deadline, uint256 currentTime);
error MarketModulePaused();
error MarketExpired();
error MarketResolved();
error MarketInRefundMode();
error MarketNotFound();
error InvalidRecipient();
error InsufficientLiquidity();           // hybrid CLOB+AMM cannot cover the trade
error ExactInUnfilled(uint256 amountIn); // neither CLOB nor AMM filled any portion
error InvalidPermitAmount();             // Permit2 amount != usdcIn
error InvalidPermitSpender();            // Permit2 spender != Router
error FinalizeBalanceNonZero();          // internal bug, report if encountered
```

In viem:

```typescript
try {
  await walletClient.writeContract({ ... });
} catch (err: any) {
  if (err.cause?.data?.errorName === 'InsufficientOutput') {
    // handle slippage
  }
}
```

## Emitted events

After a swap, the Router emits:

```solidity
event Trade(
    uint256 indexed marketId,
    address indexed trader,
    address indexed recipient,
    TradeType tradeType,    // 0=BUY_YES, 1=SELL_YES, 2=BUY_NO, 3=SELL_NO
    uint256 amountIn,
    uint256 amountOut,
    uint256 clobFilled,
    uint256 ammFilled
);

event ClobSkipped(
    uint256 indexed marketId,
    address indexed recipient,
    bytes4 reason           // 4-byte selector of the CLOB revert (or 0x0)
);
```

`Trade` is the **canonical source** for the indexer. Listen to it to update your UI after tx confirmation. `ClobSkipped` fires when the CLOB leg reverted and the trade fell back to the AMM - useful as an observability signal in your dashboards.

## Batch with Smart Account

![AA UserOp batch](../.gitbook/assets/22-aa-userop.svg)

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

* **AMM-only** (skip CLOB): call Uniswap v4's UniversalRouter directly.
* **CLOB-only** (skip AMM): call `PrediXExchange.fillMarketOrder` or `placeOrder`.

The default recommendation is to use `PrediXRouter` - it optimizes price across both sources.

## Test on local fork

```bash
# Anvil fork Unichain mainnet (current beta runs here)
anvil --fork-url https://mainnet.unichain.org

# Deploy test contract or call directly
forge script test/Integration.s.sol --fork-url http://localhost:8545
```

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
