# Tạo Market

Chỉ địa chỉ có `ADMIN_ROLE` trên Diamond mới tạo được market.

## Từng bước

### 1. Chuẩn bị tham số

```typescript
const question = "ETH sẽ vượt $10,000 trước 31/12/2026?";
const description = "Resolve YES nếu giá ETH/USD vượt $10,000 trên một sàn lớn nào đó trước 31/12/2026 23:59 UTC.";
const endTime = Math.floor(new Date("2026-12-31T23:59:00Z").getTime() / 1000);
const categoryId = ethers.ZeroHash; // Không thuộc category nào (market độc lập)
const oracle = "0x699A8C74663b1C852E195b2ffa00D5965E992Cf3"; // ManualOracleAdapter
```

### 2. Tạo market

```typescript
const DIAMOND = "0xF38a265E6e4F57D000a1CC08004da5B4A380B08A";

const diamond = new ethers.Contract(DIAMOND, [
  "function createMarket(string,string,uint256,bytes32,address) returns (bytes32)"
], signer);

const tx = await diamond.createMarket(question, description, endTime, categoryId, oracle);
const receipt = await tx.wait();

// Lấy marketId từ event MarketCreated
const event = receipt.logs.find((log: any) => {
  try {
    return diamond.interface.parseLog(log)?.name === "MarketCreated";
  } catch { return false; }
});
const marketId = diamond.interface.parseLog(event).args.marketId;
console.log(`Market đã tạo: ${marketId}`);
```

### 3. Những gì xảy ra tự động

Khi `createMarket` thực thi, Diamond sẽ:

1. Deploy hai contract token YES và NO (ERC-20)
2. Đăng ký pool YES/USDC trên Hook qua `registerMarketPool`
3. Khởi tạo pool Uniswap v4
4. Emit event `MarketCreated`

Market có thể giao dịch ngay — không cần setup thêm gì.

### 4. Verify on-chain

```typescript
const market = await diamond.getMarket(marketId);
console.log(`YES token: ${market.yesToken}`);
console.log(`NO token: ${market.noToken}`);
console.log(`Oracle: ${market.oracle}`);
```

> ⚠️ Chỉ oracle đã approve mới được gán. Nếu dùng adapter mới, gọi `setApprovedOracle` trước.

## Tạo category

```typescript
const tx = await diamond.createCategory(
  "Bầu cử Tổng thống Mỹ 2028",
  "Ứng viên nào sẽ thắng bầu cử Tổng thống Mỹ 2028?",
  endTime
);
// Sau đó thêm market vào category:
await diamond.addMarketToCategory(categoryId, marketId);
```

## Tiếp theo

- [Tích hợp giao dịch](trading-integration.md) — giao dịch trên market mới
- [Resolve & Redeem](resolve-redeem.md) — phân xử khi market kết thúc
- [Oracle](../contracts/oracle.md) — setup oracle
