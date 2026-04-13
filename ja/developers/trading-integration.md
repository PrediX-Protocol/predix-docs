---
description: Integrate PrediX trading into your application
---

# Trading Integration

## Step 1: Approve Tokens

```typescript
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

// Approve Router for market orders
await usdc.approve(ROUTER_ADDRESS, ethers.MaxUint256);

// Approve Exchange for limit orders
await usdc.approve(EXCHANGE_ADDRESS, ethers.MaxUint256);

// Approve Diamond for split/merge
await usdc.approve(DIAMOND_ADDRESS, ethers.MaxUint256);
```

## Step 2: Market Order (via Router)

```typescript
const router = new ethers.Contract(ROUTER_ADDRESS, ROUTER_ABI, signer);
const marketId = "0x...";
const deadline = Math.floor(Date.now() / 1000) + 300;

// Get quote first
const usdcIn = ethers.parseUnits("100", 6);
const expectedYes = await router.quoteBuyYes(marketId, usdcIn);
const minYesOut = expectedYes * 98n / 100n; // 2% slippage

// Execute
const tx = await router.buyYes(marketId, usdcIn, minYesOut, signer.address, deadline);
await tx.wait();
```

## Step 3: Limit Order (via Exchange)

```typescript
const exchange = new ethers.Contract(EXCHANGE_ADDRESS, EXCHANGE_ABI, signer);

// BUY_YES at $0.65
const side = 0; // BUY_YES
const price = ethers.parseUnits("0.65", 6);
const amount = ethers.parseUnits("100", 6);

const tx = await exchange.placeOrder(marketId, side, price, amount);
const receipt = await tx.wait();
```

## Step 4: Cancel Order

```typescript
const orderId = "0x..."; // From OrderPlaced event
await exchange.cancelOrder(orderId);
```

## Error Handling

```typescript
try {
  await router.buyYes(marketId, usdcIn, minYesOut, signer.address, deadline);
} catch (error: any) {
  if (error.message.includes("Market_NotFound")) {
    console.error("Invalid market ID");
  } else if (error.message.includes("Market_Ended")) {
    console.error("Market has expired");
  } else if (error.message.includes("Market_ExceedsPerTradeCap")) {
    console.error("Amount exceeds per-trade cap");
  } else if (error.message.includes("SlippageExceeded")) {
    console.error("Price moved too much, increase slippage");
  }
}
```

---

**Next**: [Split & Merge](split-merge.md) · [Resolve & Redeem](resolve-redeem.md) · [Events](events.md)
