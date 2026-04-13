---
description: Tích hợp giao dịch PrediX vào ứng dụng của bạn
---

# Tích hợp giao dịch

## Bước 1: Phê duyệt token

```typescript
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

// Phê duyệt Router cho lệnh thị trường
await usdc.approve(ROUTER_ADDRESS, ethers.MaxUint256);

// Phê duyệt Exchange cho lệnh giới hạn
await usdc.approve(EXCHANGE_ADDRESS, ethers.MaxUint256);

// Phê duyệt Diamond cho tách/gộp
await usdc.approve(DIAMOND_ADDRESS, ethers.MaxUint256);
```

## Bước 2: Lệnh thị trường (qua Router)

```typescript
const router = new ethers.Contract(ROUTER_ADDRESS, ROUTER_ABI, signer);
const marketId = "0x...";
const deadline = Math.floor(Date.now() / 1000) + 300;

// Lấy báo giá trước
const usdcIn = ethers.parseUnits("100", 6);
const expectedYes = await router.quoteBuyYes(marketId, usdcIn);
const minYesOut = expectedYes * 98n / 100n; // 2% trượt giá

// Thực hiện
const tx = await router.buyYes(marketId, usdcIn, minYesOut, signer.address, deadline);
await tx.wait();
```

## Bước 3: Lệnh giới hạn (qua Exchange)

```typescript
const exchange = new ethers.Contract(EXCHANGE_ADDRESS, EXCHANGE_ABI, signer);

// MUA YES tại $0.65
const side = 0; // BUY_YES
const price = ethers.parseUnits("0.65", 6);
const amount = ethers.parseUnits("100", 6);

const tx = await exchange.placeOrder(marketId, side, price, amount);
const receipt = await tx.wait();
```

## Bước 4: Hủy lệnh

```typescript
const orderId = "0x..."; // Từ sự kiện OrderPlaced
await exchange.cancelOrder(orderId);
```

## Xử lý lỗi

```typescript
try {
  await router.buyYes(marketId, usdcIn, minYesOut, signer.address, deadline);
} catch (error: any) {
  if (error.message.includes("Market_NotFound")) {
    console.error("ID thị trường không hợp lệ");
  } else if (error.message.includes("Market_Ended")) {
    console.error("Thị trường đã hết hạn");
  } else if (error.message.includes("Market_ExceedsPerTradeCap")) {
    console.error("Vượt quá giới hạn mỗi giao dịch");
  } else if (error.message.includes("SlippageExceeded")) {
    console.error("Giá biến động quá nhiều, hãy tăng trượt giá");
  }
}
```

---

**Tiếp theo**: [Tách & Gộp](split-merge.md) · [Giải quyết & Đổi thưởng](resolve-redeem.md) · [Sự kiện](events.md)
