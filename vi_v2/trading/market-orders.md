# Lệnh thị trường

Lệnh thị trường được khớp ngay lập tức với giá tốt nhất trên cả CLOB và AMM.

## Hàm

Mọi hàm market order nằm trên **Router** (`0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7`).

| Hàm | Hành động | Input | Output |
|----------|--------|-------|--------|
| `buyYes` | Mua YES bằng USDC | `usdcIn` | `yesOut` |
| `sellYes` | Bán YES lấy USDC | `yesIn` | `usdcOut` |
| `buyNo` | Mua NO bằng USDC | `usdcIn, mintAmount` | `noOut` |
| `sellNo` | Bán NO lấy USDC | `noIn` | `usdcOut` |

### Signatures

```solidity
buyYes(bytes32 marketId, uint256 usdcIn, uint256 minYesOut, address recipient, uint256 deadline) → uint256 yesOut
sellYes(bytes32 marketId, uint256 yesIn, uint256 minUsdcOut, address recipient, uint256 deadline) → uint256 usdcOut
buyNo(bytes32 marketId, uint256 usdcIn, uint256 mintAmount, uint256 minNoOut, address recipient, uint256 deadline) → uint256 noOut
sellNo(bytes32 marketId, uint256 noIn, uint256 minUsdcOut, address recipient, uint256 deadline) → uint256 usdcOut
```

## Ví dụ code

### Mua YES

```typescript
import { ethers } from "ethers";

const ROUTER = "0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

const usdc = new ethers.Contract(USDC, ["function approve(address,uint256)"], signer);
const router = new ethers.Contract(ROUTER, [
  "function buyYes(bytes32,uint256,uint256,address,uint256) returns (uint256)",
  "function sellYes(bytes32,uint256,uint256,address,uint256) returns (uint256)"
], signer);

// Approve USDC
await usdc.approve(ROUTER, ethers.parseUnits("50", 6));

// Mua 50 USDC worth of YES, chấp nhận slippage ~10%
const usdcIn = ethers.parseUnits("50", 6);
const minYesOut = ethers.parseUnits("45", 6);
const deadline = Math.floor(Date.now() / 1000) + 300;

const tx = await router.buyYes(marketId, usdcIn, minYesOut, myAddress, deadline);
await tx.wait();
```

### Bán YES

```typescript
// Approve YES cho Router trước
const yesToken = new ethers.Contract(yesTokenAddress, ["function approve(address,uint256)"], signer);
await yesToken.approve(ROUTER, ethers.parseUnits("50", 6));

const tx = await router.sellYes(
  marketId,
  ethers.parseUnits("50", 6),  // bán 50 YES
  ethers.parseUnits("25", 6),  // tối thiểu thu 25 USDC
  myAddress,
  deadline
);
```

## Bảo vệ slippage

- **`minOut`** — số tối thiểu bạn chịu nhận. Tx revert nếu kết quả tệ hơn.
- **`deadline`** — timestamp UNIX. Tx revert nếu chưa được mine trước thời điểm này.

> ⚠️ Luôn đặt `minOut` > 0 và deadline hợp lý. Không có slippage protection, bạn có thể nhận ít hơn kỳ vọng rất nhiều.

## Tiếp theo

- [Lệnh giới hạn](limit-orders.md) — đặt lệnh ở giá chính xác
- [Smart Routing](smart-routing.md) — cách Router chia lệnh qua venue
- [Developer: Tích hợp giao dịch](../developers/trading-integration.md) — hướng dẫn đầy đủ
