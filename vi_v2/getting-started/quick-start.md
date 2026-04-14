# Khởi động nhanh

## Dành cho trader

### 5 bước để thực hiện giao dịch đầu tiên

1. **Kết nối ví** với Unichain Sepolia (Chain ID: `1301`, RPC: `https://sepolia.unichain.org`)
2. **Lấy USDC testnet** từ [Unichain Sepolia Faucet](https://faucet.unichain.org) hoặc mint trực tiếp từ contract test USDC
3. **Approve USDC** cho Router
4. **Chọn market** và quyết định mua YES hay NO
5. **Gửi lệnh** qua Router — `buyYes()` hoặc `buyNo()`

> ⚠️ Đây là testnet. Token không có giá trị thật, chỉ dùng USDC test.

### Giao dịch qua dApp

Truy cập web app của PrediX, kết nối ví, chọn market và giao dịch trực tiếp trên UI.

### Giao dịch qua contract

```typescript
import { ethers } from "ethers";

const ROUTER = "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

// 1. Approve USDC
const usdc = new ethers.Contract(USDC, ["function approve(address,uint256)"], signer);
await usdc.approve(ROUTER, ethers.parseUnits("100", 6));

// 2. Mua YES
const router = new ethers.Contract(ROUTER, [
  "function buyYes(bytes32,uint256,uint256,address,uint256) returns (uint256)"
], signer);

const tx = await router.buyYes(
  marketId,                          // bytes32
  ethers.parseUnits("100", 6),       // bỏ vào 100 USDC
  ethers.parseUnits("90", 6),        // tối thiểu nhận 90 YES (slippage)
  await signer.getAddress(),          // địa chỉ nhận
  Math.floor(Date.now() / 1000) + 300 // deadline 5 phút
);
```

## Dành cho developer

- **Địa chỉ contract & ABIs:** [Tổng quan contracts](../contracts/overview.md)
- **Hướng dẫn tích hợp đầy đủ:** [Developer Quickstart](../developers/quickstart.md)
- **Ví dụ code:** [Tích hợp giao dịch](../developers/trading-integration.md)

## Tiếp theo

- [Tổng quan giao dịch](../trading/overview.md) — lệnh thị trường, lệnh giới hạn, routing
- [Prediction Markets 101](../concepts/prediction-markets.md) — cơ chế giá
