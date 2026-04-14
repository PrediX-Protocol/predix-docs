# Resolve & Redeem

Ví dụ code cho vòng đời sau khi giao dịch. Tổng quan khái niệm xem [Phân xử](../concepts/resolution.md).

## Flow resolve bình thường

### 1. Oracle resolve

```typescript
const ORACLE = "0x699A8C74663b1C852E195b2ffa00D5965E992Cf3";

// Admin gọi (ManualOracleAdapter)
const oracle = new ethers.Contract(ORACLE, [
  "function resolve(bytes32,bool)"
], adminSigner);

await oracle.resolve(marketId, true); // YES thắng
```

### 2. Finalize resolution

```typescript
const diamond = new ethers.Contract(DIAMOND, [
  "function resolveMarket(bytes32)"
], signer);

// Bất kỳ ai cũng gọi được sau khi oracle đã resolve và endTime đã qua
await diamond.resolveMarket(marketId);
```

### 3. Redeem tiền thắng

```typescript
const diamond = new ethers.Contract(DIAMOND, [
  "function redeemMarketTokens(bytes32)"
], signer);

// Holder token thắng nhận $1 USDC mỗi token
const tx = await diamond.redeemMarketTokens(marketId);
const receipt = await tx.wait();
// Parse event TokensRedeemed để lấy payout
```

## Emergency Resolution

```typescript
// OPERATOR_ROLE — chỉ gọi được sau 7 ngày kể từ endTime
const diamond = new ethers.Contract(DIAMOND, [
  "function emergencyResolve(bytes32,bool)"
], operatorSigner);

await diamond.emergencyResolve(marketId, false); // NO thắng
```

## Refund mode

```typescript
// OPERATOR_ROLE bật refund
await diamond.enableRefundMode(marketId);

// Mọi holder claim phần refund tỷ lệ
const diamond = new ethers.Contract(DIAMOND, [
  "function refund(bytes32)"
], userSigner);

await diamond.refund(marketId);
```

> ⚠️ Refund mode không đảo ngược được. Market không còn resolve bình thường được nữa.

## Check trạng thái resolution

```typescript
const oracle = new ethers.Contract(ORACLE, [
  "function getResolution(bytes32) view returns (bool,bool)"
], provider);

const [resolved, outcome] = await oracle.getResolution(marketId);
console.log(`Resolved: ${resolved}, Kết quả: ${outcome ? "YES" : "NO"}`);
```

## Tiếp theo

- [Events](events.md) — nghe `MarketResolved`, `TokensRedeemed`
- [Oracle](../contracts/oracle.md) — chi tiết oracle adapter
- [Errors](errors.md) — mã lỗi resolution
