# Resolve & Redeem

Code examples for the post-trading lifecycle. For concept overview, see [Resolution](../concepts/resolution.md).

## Normal Resolution Flow

### 1. Oracle Resolves

```typescript
const ORACLE = "0x699A8C74663b1C852E195b2ffa00D5965E992Cf3";

// Admin calls (ManualOracleAdapter)
const oracle = new ethers.Contract(ORACLE, [
  "function resolve(bytes32,bool)"
], adminSigner);

await oracle.resolve(marketId, true); // YES wins
```

### 2. Finalize Resolution

```typescript
const diamond = new ethers.Contract(DIAMOND, [
  "function resolveMarket(bytes32)"
], signer);

// Anyone can call after oracle has resolved and endTime has passed
await diamond.resolveMarket(marketId);
```

### 3. Redeem Winnings

```typescript
const diamond = new ethers.Contract(DIAMOND, [
  "function redeemMarketTokens(bytes32)"
], signer);

// Winning token holders receive $1 USDC per token
const tx = await diamond.redeemMarketTokens(marketId);
const receipt = await tx.wait();
// Parse TokensRedeemed event for payout amount
```

## Emergency Resolution

```typescript
// OPERATOR_ROLE — only callable 7 days after endTime
const diamond = new ethers.Contract(DIAMOND, [
  "function emergencyResolve(bytes32,bool)"
], operatorSigner);

await diamond.emergencyResolve(marketId, false); // NO wins
```

## Refund Mode

```typescript
// OPERATOR_ROLE enables refund
await diamond.enableRefundMode(marketId);

// Any token holder claims proportional refund
const diamond = new ethers.Contract(DIAMOND, [
  "function refund(bytes32)"
], userSigner);

await diamond.refund(marketId);
```

> ⚠️ Refund mode is irreversible. The market cannot be resolved normally after this.

## Check Resolution Status

```typescript
const oracle = new ethers.Contract(ORACLE, [
  "function getResolution(bytes32) view returns (bool,bool)"
], provider);

const [resolved, outcome] = await oracle.getResolution(marketId);
console.log(`Resolved: ${resolved}, Outcome: ${outcome ? "YES" : "NO"}`);
```

## Next Steps

- [Events](events.md) — listen for `MarketResolved`, `TokensRedeemed`
- [Contract: Oracle](../contracts/oracle.md) — oracle adapter details
- [Errors](errors.md) — resolution error codes
