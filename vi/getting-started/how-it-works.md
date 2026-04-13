---
description: Quy trình từng bước từ tạo market đến nhận token
---

# Cách hoạt động

## Vòng đời market

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  Tạo     │ →  │  Hoạt    │ →  │  Hết hạn │ →  │ Giải     │ →  │  Nhận   │
│          │    │  động    │    │          │    │ quyết    │    │  thưởng │
└──────────┘    └──────────┘    └──────────┘    └──────────┘    └──────────┘
                     │                                │
                  Giao dịch                      Khẩn cấp (7 ngày)
                  được phép                      hoặc Chế độ hoàn tiền
```

## Từng bước

### 1. Tạo market

Admin tạo market với câu hỏi, thời gian kết thúc và oracle:

```
"BTC có vượt $100,000 trước 31/12/2026 không?"
  → Token YES + Token NO + Pool AMM Uniswap v4 (tự động khởi tạo ở $0.50)
```

### 2. Giao dịch

Người dùng có được token kết quả bằng cách giao dịch:

- **Tách**: Gửi 1 USDC → nhận 1 YES + 1 NO token
- **Mua qua Router**: Trả USDC → Smart Router tìm giá tốt nhất qua CLOB + AMM → nhận token YES hoặc NO
- **Lệnh giới hạn**: Đặt lệnh trên CLOB ở giá cụ thể

### 3. Khám phá giá

Giá token phản ánh quan điểm xác suất của thị trường:

| Giá YES | Ý nghĩa |
| --------- | -------------- |
| $0.70 | Thị trường tin có 70% khả năng kết quả YES |
| $0.30 | Thị trường tin có 30% khả năng kết quả YES |

> ⚠️ **Bất biến quan trọng**: Giá YES + Giá NO ≈ $1.00 (duy trì bởi chênh lệch tách/gộp)

### 4. Giải quyết

Sau khi sự kiện xảy ra:

1. Oracle (Manual hoặc Chainlink) báo cáo kết quả
2. Bất kỳ ai gọi `resolveMarket(marketId)` để xác nhận cuối cùng

### 5. Nhận thưởng

Người giữ token thắng nhận $1.00 mỗi token:

```
Nếu YES thắng:
  1 token YES → $1.00 USDC
  1 token NO  → $0.00

Nếu NO thắng:
  1 token YES → $0.00
  1 token NO  → $1.00 USDC
```

## Ví dụ thực tế

Alice nghĩ BTC sẽ vượt $100K. Giá YES hiện tại: $0.65.

| Hành động | Alice trả | Alice nhận | L/L |
| ------ | ---------- | -------------- | --- |
| Mua 100 YES @ $0.65 | 65 USDC | 100 YES | — |
| BTC vượt $100K ✅ | — | — | — |
| Nhận 100 YES | — | 100 USDC | **+35 USDC (lãi 54%)** |

Nếu BTC KHÔNG vượt $100K, 100 token YES của Alice có giá trị $0. Lỗ: 65 USDC.

---

**Tiếp theo**: [Bắt đầu nhanh](quick-start.md) · [Token kết quả](../concepts/outcome-tokens.md) · [Tách & Gộp](../concepts/split-and-merge.md)
