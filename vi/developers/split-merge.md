---
description: Tách USDC thành token và gộp token trở lại
---

# Tách & Gộp (Hướng dẫn dành cho nhà phát triển)

## Tách vị thế

```typescript
const diamond = new ethers.Contract(DIAMOND_ADDRESS, DIAMOND_ABI, signer);
const usdc = new ethers.Contract(USDC_ADDRESS, ERC20_ABI, signer);

const marketId = "0x...";
const amount = ethers.parseUnits("100", 6); // 100 USDC

// Phê duyệt USDC cho Diamond
await (await usdc.approve(DIAMOND_ADDRESS, amount)).wait();

// Tách: 100 USDC → 100 YES + 100 NO
const tx = await diamond.splitPosition(marketId, amount);
await tx.wait();

// Kiểm tra số dư
const market = await diamond.getMarket(marketId);
const yesToken = new ethers.Contract(market.yesToken, ERC20_ABI, provider);
const noToken = new ethers.Contract(market.noToken, ERC20_ABI, provider);

console.log("Số dư YES:", ethers.formatUnits(await yesToken.balanceOf(signer.address), 6));
console.log("Số dư NO:", ethers.formatUnits(await noToken.balanceOf(signer.address), 6));
```

## Gộp vị thế

```typescript
const mergeAmount = ethers.parseUnits("50", 6); // Gộp 50 YES + 50 NO

// Không cần phê duyệt (Diamond đã có quyền đốt)
const tx = await diamond.mergePositions(marketId, mergeAmount);
await tx.wait();

// Kết quả: 50 USDC được trả về ví của bạn
```

> ⚠️ Bạn phải nắm giữ số lượng **bằng nhau** của token YES và NO để gộp. Số lượng gộp không được vượt quá số dư của bất kỳ token nào.

---

**Tiếp theo**: [Giải quyết & Đổi thưởng](resolve-redeem.md) · [Sự kiện](events.md)
