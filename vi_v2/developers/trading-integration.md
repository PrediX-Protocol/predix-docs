# Tích hợp giao dịch

Hướng dẫn đầy đủ để tích hợp giao dịch PrediX vào ứng dụng của bạn.

## Bước 1: Approve token

Trước khi giao dịch, approve contract liên quan được dùng token của bạn.

```typescript
const ROUTER = "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7";
const EXCHANGE = "0xa202abCb2A358c0862B2dA76b553398339F2C638";
const DIAMOND = "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

const usdc = new ethers.Contract(USDC, ["function approve(address,uint256)"], signer);

// Approve tuỳ theo mục đích:
await usdc.approve(ROUTER, ethers.MaxUint256);    // Lệnh thị trường (buy YES/NO)
await usdc.approve(EXCHANGE, ethers.MaxUint256);   // Lệnh giới hạn (phía mua)
await usdc.approve(DIAMOND, ethers.MaxUint256);    // Split vị thế
```

> ⚠️ Với lệnh bán, hãy approve YES/NO tương ứng cho Router hoặc Exchange.

## Bước 2: Lấy quote

Luôn lấy quote trước khi execute. Xem [Smart Routing](../trading/smart-routing.md) để hiểu rõ.

```typescript
const router = new ethers.Contract(ROUTER, [
  "function quoteBuyYes(bytes32,uint256) view returns (uint256)",
], provider);

const expected = await router.quoteBuyYes(marketId, ethers.parseUnits("100", 6));
console.log(`Dự kiến: ${ethers.formatUnits(expected, 6)} YES`);
```

## Bước 3: Gửi lệnh

Lệnh thị trường xem [Lệnh thị trường](../trading/market-orders.md). Lệnh giới hạn xem [Lệnh giới hạn](../trading/limit-orders.md).

## Bước 4: Huỷ lệnh

```typescript
const exchange = new ethers.Contract(EXCHANGE, [
  "function cancelOrder(bytes32)"
], signer);

await exchange.cancelOrder(orderId);
```

## Xử lý lỗi

```typescript
try {
  const tx = await router.buyYes(marketId, usdcIn, minOut, recipient, deadline);
  await tx.wait();
} catch (error: any) {
  if (error.message.includes("Market_NotFound")) {
    console.error("Market ID không hợp lệ");
  } else if (error.message.includes("Market_Ended")) {
    console.error("Market đã hết hạn");
  } else if (error.message.includes("Market_ExceedsPerTradeCap")) {
    console.error("Lệnh vượt quá giới hạn");
  }
  // Danh sách lỗi đầy đủ: developers/errors
}
```

Split/merge xem [Split & Merge](../concepts/split-and-merge.md). Phân xử và redeem xem [Resolve & Redeem](resolve-redeem.md).

## Tiếp theo

- [Events](events.md) — nghe event giao dịch
- [Errors](errors.md) — tham chiếu lỗi đầy đủ
- [Resolve & Redeem](resolve-redeem.md) — flow sau khi resolve
