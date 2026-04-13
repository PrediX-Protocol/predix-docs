---
description: Bắt đầu giao dịch hoặc phát triển trên PrediX trong vài phút
---

# Bắt đầu nhanh

## Cho trader

### Bước 1: Kết nối ví

Thêm Unichain Sepolia vào ví:

| Thông số | Giá trị |
| --------- | ----- |
| Network Name | Unichain Sepolia |
| RPC URL | `https://sepolia.unichain.org` |
| Chain ID | `1301` |
| Currency Symbol | ETH |
| Block Explorer | `https://sepolia.uniscan.xyz` |

### Bước 2: Nhận USDC thử nghiệm

Nhận USDC testnet từ PrediX faucet hoặc mint trực tiếp trên testnet.

### Bước 3: Phê duyệt USDC

Phê duyệt contract Router để sử dụng USDC của bạn:

```
USDC Address: 0x12fd156C8b5F2901BA2781d97db84AaC56b2b911
Router Address: 0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7
```

### Bước 4: Giao dịch

- **Mua YES**: Gọi `router.buyYes(marketId, usdcAmount, minYesOut, yourAddress, deadline)`
- **Mua NO**: Gọi `router.buyNo(marketId, usdcAmount, mintAmount, minNoOut, yourAddress, deadline)`

### Bước 5: Nhận thưởng sau khi giải quyết

Khi market được giải quyết, gọi `diamond.redeemMarketTokens(marketId)` để nhận thưởng.

---

## Cho nhà phát triển

### Địa chỉ contract (Unichain Sepolia)

| Contract | Địa chỉ |
| -------- | ------- |
| Diamond | `0xF38a265E6e4F57D000a1CC08004da5B4A380B08A` |
| Hook | `0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0` |
| Exchange | `0xa202abCb2A358c0862B2dA76b553398339F2C638` |
| Router | `0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7` |
| Oracle | `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3` |
| USDC | `0x12fd156C8b5F2901BA2781d97db84AaC56b2b911` |
| Pool Manager | `0x00B036B58a818B1BC34d502D3fE730Db729e62AC` |

### Ví dụ code nhanh

```typescript
import { ethers } from "ethers";

const provider = new ethers.JsonRpcProvider("https://sepolia.unichain.org");
const signer = new ethers.Wallet(PRIVATE_KEY, provider);

const diamond = new ethers.Contract(
  "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A",
  DIAMOND_ABI,
  signer
);

// Đọc market
const market = await diamond.getMarket(marketId);
console.log("Question:", market.question);
console.log("YES token:", market.yesToken);
console.log("Total collateral:", ethers.formatUnits(market.totalCollateral, 6));
```

---

**Tiếp theo**: [Hướng dẫn nhà phát triển](../developers/quickstart.md) · [Địa chỉ contract](../developers/addresses.md) · [Tích hợp giao dịch](../developers/trading-integration.md)
