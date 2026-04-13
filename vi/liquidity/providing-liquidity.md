---
description: Huong dan tung buoc cung cap thanh khoan AMM
---

# Cung cap Thanh khoan

## Tong quan

Thanh khoan AMM duoc cung cap cho pool YES/USDC cua Uniswap v4 thong qua PositionManager.

## Cac buoc

### 1. Tach USDC thanh YES + NO

```typescript
const amount = ethers.parseUnits("1000", 6); // 1000 USDC
await usdc.approve(DIAMOND_ADDRESS, amount);
await diamond.splitPosition(marketId, amount);
// Bay gio ban co 1000 YES + 1000 NO
```

### 2. Phe duyet YES va USDC cho PositionManager

```typescript
const yesToken = new ethers.Contract(market.yesToken, ERC20_ABI, signer);
await yesToken.approve(POSITION_MANAGER, ethers.MaxUint256);
await usdc.approve(POSITION_MANAGER, ethers.MaxUint256);
```

### 3. Mint vi the LP

Su dung Uniswap v4 PositionManager de tao vi the thanh khoan trong pool YES/USDC. Ban co the chon:

- **Toan bo pham vi**: Bao phu $0.01–$0.99, hieu qua von thap hon
- **Tap trung**: Bao phu pham vi hep (vi du: $0.40–$0.70), hieu qua von cao hon

### 4. Khi nao nen rut

- Rut LP **it nhat 24 gio** truoc khi thi truong het han (khi phi tang len 5%)
- Theo doi thi truong — neu ket qua tro nen chac chan som, rut ngay lap tuc
- Sau khi quyet toan, token thua co gia tri $0

> ⚠️ **Luu y**: Token NO tu viec tach co the ban hoac giu rieng. Chung khong can thiet cho LP.

---

**Tiep theo**: [Tao lap Thi truong](market-making.md) · [Tong quan Thanh khoan](overview.md)
