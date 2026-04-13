---
description: Điều gì xảy ra khi market kết thúc
---

# Khi market kết thúc thì sao?

## Dòng thời gian

```
1. Tạo market         → Mở giao dịch
2. Giai đoạn giao dịch → Mua/bán cổ phần YES và NO
3. Market hết hạn     → Dừng giao dịch (đạt endTime)
4. Giải quyết         → Oracle xác định kết quả (YES hoặc NO)
5. Nhận thưởng        → Người thắng nhận $1.00 mỗi cổ phần thắng
```

## Từng bước

### 1. Market hết hạn

Khi đếm ngược về 0, **không thể giao dịch thêm**. Bạn không thể mua hoặc bán cổ phần mới.

### 2. Oracle giải quyết

**Oracle** xác định kết quả thế giới thực:

- **Market giá** (vd: "BTC > $100K?"): Chainlink price feed kiểm tra tự động
- **Market sự kiện** (vd: "Ai thắng bầu cử?"): Admin xác minh và gửi kết quả
- Thường xảy ra trong vài phút đến vài giờ sau khi hết hạn

### 3. Nhận thưởng

Nếu bạn giữ cổ phần **thắng**:

```
Nhấn "Nhận thưởng" → Cổ phần thắng bị đốt → USDC gửi vào ví

100 cổ phần thắng → $100 USDC
```

Nếu bạn giữ cổ phần **thua**:

```
Cổ phần có giá trị = $0. Không cần làm gì.
Chúng đơn giản là không có giá trị.
```

## Nếu muốn thoát sớm?

Bạn **không cần chờ** giải quyết. Bạn có thể bán cổ phần bất cứ lúc nào trước khi market hết hạn.

```
Mua YES ở $0.30 → Giá hiện tại $0.60
→ Bán ngay ở $0.60 → Chốt lời ngay lập tức
→ Không cần chờ giải quyết
```

## Nếu oracle sai thì sao?

PrediX có các cơ chế an toàn:

| Cơ chế | Chức năng |
|-----------|-------------|
| **Giải quyết khẩn cấp** | Admin có thể ghi đè sau 7 ngày |
| **Chế độ hoàn tiền** | Mọi người nhận hoàn tiền theo tỷ lệ nếu market không hợp lệ |

> 💡 Market được thiết kế để giải quyết chính xác. Cơ chế khẩn cấp là mạng an toàn, hiếm khi được sử dụng.

## Market đa kết quả

Với market có nhiều kết quả (vd: "Ai thắng bầu cử?"):

```
5 ứng viên: Trump, DeSantis, Newsom, Harris, Other
→ Admin chọn người thắng (vd: index 0 = Trump)
→ Cổ phần YES của Trump = $1.00
→ Tất cả cổ phần YES khác = $0.00
→ Tất cả cổ phần NO khác = $1.00 (vì các ứng viên đó không thắng)
```

---

**Tiếp theo**: [Rủi ro & Mẹo](risks-and-tips.md) · [Giao dịch đầu tiên](../user-guide/getting-started/first-trade.md)
