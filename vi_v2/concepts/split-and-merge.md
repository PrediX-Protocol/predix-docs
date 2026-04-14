# Split & Merge

Split và merge là cơ chế nền tảng để tạo và huỷ token kết quả, đồng thời neo giá về bất biến $1.

## Split: USDC → YES + NO

Nạp USDC vào Diamond để nhận số lượng bằng nhau token YES và NO.

```
$100 USDC → 100 YES + 100 NO
```

## Merge: YES + NO → USDC

Trả về Diamond số lượng bằng nhau YES và NO để nhận lại USDC.

```
100 YES + 100 NO → $100 USDC
```

## Tại sao quan trọng

1. **Neo giá:** Đảm bảo YES + NO ≈ $1.00 nhờ arbitrage
2. **Tạo thanh khoản:** Ai cũng có thể tạo token mà không cần đối tác
3. **Hiệu quả giá:** Sai lệch giá bị arbitrager chỉnh ngay lập tức

### Ví dụ arbitrage

Giả sử YES = $0.55, NO = $0.40 → tổng = $0.95 (dưới $1).

Arbitrager **mua** cả hai (tổng chi $0.95), **merge** về USDC → nhận $1.00. Lãi: $0.05 cho mỗi cặp.

## Ví dụ code

```typescript
import { ethers } from "ethers";

const DIAMOND = "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A";
const USDC = "0x12fd156C8b5F2901BA2781d97db84AaC56b2b911";

// Approve USDC cho Diamond
const usdc = new ethers.Contract(USDC, ["function approve(address,uint256)"], signer);
await usdc.approve(DIAMOND, ethers.parseUnits("100", 6));

const diamond = new ethers.Contract(DIAMOND, [
  "function splitPosition(bytes32,uint256)",
  "function mergePositions(bytes32,uint256)"
], signer);

// Split: 100 USDC → 100 YES + 100 NO
await diamond.splitPosition(marketId, ethers.parseUnits("100", 6));

// Merge: 100 YES + 100 NO → 100 USDC
// (cần approve cả YES và NO cho Diamond trước)
await diamond.mergePositions(marketId, ethers.parseUnits("100", 6));
```

> ⚠️ Để merge, bạn phải approve **cả YES và NO** cho Diamond trước khi gọi `mergePositions`.

## Thao tác nhóm

Với [thị trường nhiều kết quả](multi-outcome-markets.md), dùng `groupSplit` và `groupMerge` để thao tác trên tất cả các market trong một category cùng lúc.

## Tiếp theo

- [Phân xử kết quả](resolution.md) — chuyện gì xảy ra khi market kết thúc
- [Phí](fees.md) — biểu phí AMM và CLOB
- [Tổng quan giao dịch](../trading/overview.md) — mua bán token
