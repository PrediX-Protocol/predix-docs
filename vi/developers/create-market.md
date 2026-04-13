---
description: Tạo thị trường dự đoán bằng lập trình
---

# Tạo thị trường

> ⚠️ Tạo thị trường yêu cầu **ADMIN_ROLE**.

## Hướng dẫn từng bước

```typescript
import { ethers } from "ethers";

const diamond = new ethers.Contract(DIAMOND_ADDRESS, DIAMOND_ABI, signer);

// Tham số
const question = "Will BTC exceed $100,000 by December 31, 2026?";
const description = "Resolves YES if Bitcoin price (Chainlink BTC/USD) >= $100,000.";
const endTime = Math.floor(new Date("2026-12-31T23:59:59Z").getTime() / 1000);
const categoryId = ethers.ZeroHash; // Không có danh mục (thị trường độc lập)
const oracle = "0x699A8C74663b1C852E195b2ffa00D5965E992Cf3"; // ManualOracle

// Tạo
const tx = await diamond.createMarket(question, description, endTime, categoryId, oracle);
const receipt = await tx.wait();

// Phân tích sự kiện MarketCreated
const event = receipt.logs.find(
  (log) => diamond.interface.parseLog(log)?.name === "MarketCreated"
);
const parsed = diamond.interface.parseLog(event);
const marketId = parsed.args.marketId;

console.log("ID thị trường:", marketId);
console.log("Token YES:", parsed.args.yesToken);
console.log("Token NO:", parsed.args.noToken);
```

## Các thao tác được thực hiện tự động

Khi `createMarket` được gọi:

1. Token ERC-20 YES và NO được triển khai (CREATE2)
2. Pool AMM Uniswap v4 (YES/USDC) được tạo và khởi tạo tại $0.50
3. Pool được đăng ký với Hook
4. Dữ liệu thị trường được lưu trữ trong Diamond storage

## Tham số

| Tham số | Kiểu | Mô tả |
| --------- | ---- | ----------- |
| `question` | string | Câu hỏi thị trường |
| `description` | string | Thông tin bổ sung |
| `endTime` | uint256 | Mốc thời gian Unix khi giao dịch dừng |
| `categoryId` | bytes32 | ID danh mục (hoặc `bytes32(0)` cho thị trường độc lập) |
| `oracle` | address | Địa chỉ bộ điều hợp oracle đã được phê duyệt |

## Xác minh trên chuỗi

```typescript
const market = await diamond.getMarket(marketId);
console.log("Tồn tại:", await diamond.isMarketExists(marketId));
console.log("Oracle:", market.oracle);
console.log("Thời gian kết thúc:", new Date(Number(market.endTime) * 1000));
```

---

**Tiếp theo**: [Tích hợp giao dịch](trading-integration.md) · [Tách & Gộp](split-merge.md)
