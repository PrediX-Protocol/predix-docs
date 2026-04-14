# Trading Integration

Complete guide to integrating PrediX trading into your application.

## Step 1: Approve Tokens

Before trading, approve the relevant contract to spend your tokens.

```typescript
const ROUTER = "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7";
const EXCHANGE = "0xa202abCb2A358c0862B2dA76b553398339F2C638";
const DIAMOND = "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

const usdc = new ethers.Contract(USDC, ["function approve(address,uint256)"], signer);

// Approve based on what you want to do:
await usdc.approve(ROUTER, ethers.MaxUint256);    // Market orders (buy YES/NO)
await usdc.approve(EXCHANGE, ethers.MaxUint256);   // Limit orders (buy side)
await usdc.approve(DIAMOND, ethers.MaxUint256);    // Split positions
```

> ⚠️ For sell orders, approve the corresponding YES/NO token to the Router or Exchange.

## Step 2: Get a Quote

Always get a quote before executing. See [Smart Routing](../trading/smart-routing.md) for details.

```typescript
const router = new ethers.Contract(ROUTER, [
  "function quoteBuyYes(bytes32,uint256) view returns (uint256)",
], provider);

const expected = await router.quoteBuyYes(marketId, ethers.parseUnits("100", 6));
console.log(`Expected: ${ethers.formatUnits(expected, 6)} YES`);
```

## Step 3: Execute Trade

For market orders, see [Market Orders](../trading/market-orders.md). For limit orders, see [Limit Orders](../trading/limit-orders.md).

## Step 4: Cancel Orders

```typescript
const exchange = new ethers.Contract(EXCHANGE, [
  "function cancelOrder(bytes32)"
], signer);

await exchange.cancelOrder(orderId);
```

## Error Handling

```typescript
try {
  const tx = await router.buyYes(marketId, usdcIn, minOut, recipient, deadline);
  await tx.wait();
} catch (error: any) {
  if (error.message.includes("Market_NotFound")) {
    console.error("Invalid market ID");
  } else if (error.message.includes("Market_Ended")) {
    console.error("Market has expired");
  } else if (error.message.includes("Market_ExceedsPerTradeCap")) {
    console.error("Trade exceeds maximum allowed size");
  }
  // See full error list: developers/errors
}
```

For split/merge operations, see [Split & Merge](../concepts/split-and-merge.md). For resolution and redemption, see [Resolve & Redeem](resolve-redeem.md).

## Next Steps

- [Events](events.md) — listen for trade events
- [Errors](errors.md) — full error reference
- [Resolve & Redeem](resolve-redeem.md) — post-resolution flow
