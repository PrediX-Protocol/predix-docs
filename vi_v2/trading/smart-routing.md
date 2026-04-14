# Smart Routing

Router tổng hợp thanh khoản từ cả CLOB và AMM để cho kết quả tốt nhất.

## Cơ chế

1. **Check CLOB:** Router quét sổ lệnh Exchange tìm lệnh giới hạn phù hợp
2. **Khớp từ CLOB:** Thực thi với lệnh có sẵn ở giá của chúng
3. **Chuyển phần dư sang AMM:** Phần chưa khớp được gửi sang pool Uniswap v4

### Ví dụ

Mua 100 YES bằng USDC. CLOB có sẵn 60 YES trong khoảng $0.55–$0.60. Giá AMM là $0.62.

| Nguồn | Số lượng | Giá | Chi phí |
|--------|--------|-------|------|
| CLOB | 60 YES | $0.55–$0.60 trung bình | ~$34.50 |
| AMM | 40 YES | $0.62–$0.65 | ~$25.40 |
| **Tổng** | **100 YES** | **~$0.599** | **~$59.90** |

Nếu không có Router, mua toàn bộ 100 từ AMM sẽ tốn hơn vì price impact.

## Hàm quote

Lấy ước tính kết quả trước khi commit:

```typescript
const router = new ethers.Contract(ROUTER, [
  "function quoteBuyYes(bytes32,uint256) view returns (uint256)",
  "function quoteSellYes(bytes32,uint256) view returns (uint256)",
  "function quoteBuyNo(bytes32,uint256) view returns (uint256)",
  "function quoteSellNo(bytes32,uint256) view returns (uint256)"
], provider);

// 50 USDC mua được bao nhiêu YES?
const yesOut = await router.quoteBuyYes(marketId, ethers.parseUnits("50", 6));
console.log(`Kỳ vọng: ${ethers.formatUnits(yesOut, 6)} YES`);
```

## Khi nào CLOB vs AMM tốt hơn

| Tình huống | Venue tốt hơn | Lý do |
|----------|-------------|-----|
| Lệnh lớn, sổ lệnh mỏng | AMM | Giá đồng đều, không khớp lẻ |
| Cần giá chính xác | CLOB | Giá chuẩn qua limit order |
| Lệnh nhỏ, sổ sâu | CLOB | Giá tốt hơn, không tốn phí AMM |
| Gần hết hạn (AMM fee cao) | CLOB | AMM fee lên tới 5% |

## Tiếp theo

- [Matching Engine](matching-engine.md) — thuật toán khớp lệnh CLOB
- [Phí](../concepts/fees.md) — phí ảnh hưởng quyết định routing
- [Router](../contracts/router.md) — chi tiết kỹ thuật
