# Token kết quả

YES và NO là token **ERC-20** chuẩn có hỗ trợ **ERC-2612 Permit**, được Diamond contract deploy riêng cho mỗi market.

## Đặc tả

| Thuộc tính | Giá trị |
|----------|-------|
| Chuẩn | ERC-20 + ERC-2612 Permit |
| Decimals | 6 (khớp với USDC) |
| Ai mint được | Chỉ Diamond (`onlyFactory`) |
| Ai burn được | Chỉ Diamond (khi merge/redeem) |
| Khoảng giá | $0.01 – $0.99 |
| Có thể chuyển | Có — transfer ERC-20 chuẩn |

## Bất biến cung token

```
supply YES = supply NO = tổng collateral (USDC)
```

Mỗi token YES tồn tại luôn có một token NO tương ứng. Cả hai được bảo chứng 1:1 bằng USDC khoá trong Diamond. Bất biến này được cơ chế split/merge đảm bảo — xem [Split & Merge](split-and-merge.md).

## Khả năng kết hợp DeFi

Vì token là ERC-20:

- **LP trên Uniswap v4** — vừa giữ vị thế dự đoán vừa ăn phí swap
- **Approve không tốn gas** — dùng `permit()` thay vì `approve()` cho UX tốt hơn
- **Chuyển tự do** — gửi sang ví khác, dùng trong multisig, tích hợp vào vault
- **Tương lai:** làm collateral cho lending, structured product

> ⚠️ Chỉ Diamond mới mint/burn được token kết quả. Chuyển thẳng token vào địa chỉ Diamond sẽ mất vĩnh viễn.

## Tiếp theo

- [Split & Merge](split-and-merge.md) — cơ chế tạo/huỷ token
- [Tổng quan giao dịch](../trading/overview.md) — cách mua bán
- [Diamond](../contracts/diamond.md) — kiến trúc mint
