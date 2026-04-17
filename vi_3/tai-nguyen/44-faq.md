# 44. FAQ

## PrediX Protocol — Câu Hỏi Thường Gặp

***

### 🔰 Bắt Đầu

**PrediX là gì?**

PrediX là sàn dự đoán phi tập trung — nơi bạn có thể dự đoán bằng USDC vào kết quả của bất kỳ sự kiện nào: thể thao, chính trị, crypto, văn hoá và nhiều hơn nữa. Mọi giao dịch đều minh bạch, on-chain, không cần tin tưởng vào bất kỳ bên trung gian nào.

> _"Where narratives become markets"_ — Bất kỳ câu chuyện nào đang hot đều có thể trở thành một market để giao dịch.

***

**Làm sao để bắt đầu?**

Chỉ cần 4 bước:

```
1. Kết nối ví (MetaMask, Rabby...)
2. Nạp USDC vào tài khoản
3. Chọn market bạn quan tâm
4. Mua YES (tin xảy ra) hoặc NO (tin không xảy ra)
```

Sau khi sự kiện kết thúc và có kết quả → vào app nhấn **"Rút thưởng"** để nhận USDC về ví.

***

**Tôi có thể mất nhiều hơn số tiền bỏ vào không?**

Không. Mức sai lỗ tối đa là **100% số tiền bạn đặt** — không hơn. PrediX không có đòn bẩy, không có liquidation, không có rủi ro vượt vốn như trading phái sinh.

***

### 💰 Giao Dịch & Giá Cả

**Khi market có kết quả, tôi nhận tiền như thế nào?**

* Token của **phe đúng** → đổi được **$1 USDC mỗi token**
* Token của **phe sai**→ giá trị **$0**

Vào app, chọn market đã kết thúc và nhấn **"Rút thưởng"** — USDC sẽ về ví của bạn ngay.

> 💡 Ví dụ: Mua 100 token YES ở giá $0.40 (bỏ ra $40). Nếu YES đúng → nhận về $100, lời $60.

***

**Giá token được xác định như thế nào?**

Giá token phản ánh **xác suất thị trường tin rằng sự kiện sẽ xảy ra**. Giá hình thành tự nhiên qua cung/cầu trên hai nguồn thanh khoản:

* **Sổ lệnh (CLOB):** Người mua và người bán đặt lệnh, khớp với nhau
* **Bể thanh khoản tự động (AMM):** Luôn sẵn sàng khớp lệnh tức thì

> 💡 Token YES ở giá $0.70 = thị trường đang cho sự kiện đó **70% khả năng xảy ra**.

***

**Split và Merge là gì?**

Đây là cơ chế neo giá đảm bảo YES + NO luôn có tổng xấp xỉ $1:

| Thao tác  | Đầu vào                  | Đầu ra                   |
| --------- | ------------------------ | ------------------------ |
| **Split** | $1 USDC                  | 1 token YES + 1 token NO |
| **Merge** | 1 token YES + 1 token NO | $1 USDC                  |

Nhờ cơ chế này, nếu YES = $0.65 thì NO luôn xấp xỉ $0.35 — tự động cân bằng bởi thị trường.

***

**Phí giao dịch là bao nhiêu?**

| Loại                       | Phí                                                              |
| -------------------------- | ---------------------------------------------------------------- |
| Giao dịch qua AMM          | 0.5% – 5% (tự động điều chỉnh theo thời gian còn lại của market) |
| Đặt lệnh giới hạn — Maker  | **Miễn phí**                                                     |
| Khớp lệnh giới hạn — Taker | 0% – 1%                                                          |
| Rút thưởng sau khi đúng    | **Miễn phí**                                                     |

> 💡 Phí AMM tăng dần khi market gần đến ngày kết thúc — khuyến khích giao dịch sớm sẽ có phí thấp hơn.

***

### 🔒 Bảo Mật & Kỹ Thuật

**PrediX đã được kiểm tra bảo mật chưa?**

Có. Smart contract của PrediX đã trải qua quá trình kiểm tra kỹ lưỡng:

* ✅ **264 unit tests** bao phủ toàn bộ chức năng
* ✅ **3 vòng AI audit** độc lập kiểm chéo lẫn nhau
* ✅ **Slither static analysis** — 0 lỗi nghiêm trọng
* ✅ **Fuzz testing** — 14 bộ × 10,000 lần chạy ngẫu nhiên
* ✅ **31 end-to-end tests** thực thi trực tiếp on-chain

***

**PrediX đang chạy trên blockchain nào?**

| Giai đoạn      | Network                  | Chain ID |
| -------------- | ------------------------ | -------- |
| **Hiện tại**   | Unichain Sepolia Testnet | 1301     |
| **Sắp ra mắt** | Unichain Mainnet         | Q2/2026  |

Unichain là Layer 2 được xây dựng bởi đội ngũ Uniswap — tốc độ nhanh, phí rẻ, tích hợp sẵn hệ sinh thái Uniswap v4.

***

**Lấy USDC testnet để thử ở đâu?**

Hai cách:

1. Dùng **Unichain Sepolia faucet** để nhận USDC test miễn phí
2. Liên hệ trực tiếp team qua **Discord** để được hỗ trợ

***

### ❓ Câu Hỏi Khác

**Tôi có thể bán token trước khi có kết quả không?**

Có. Bạn không cần chờ đến khi market kết thúc. Bất cứ lúc nào trong thời gian market còn mở, bạn đều có thể **bán token YES hoặc NO** để chốt lời hoặc cắt lỗ sớm.

***

**Ai tạo ra các market trên PrediX?**

Hiện tại các market được tạo bởi đội ngũ PrediX và các đối tác được uỷ quyền, đảm bảo chất lượng nguồn dữ liệu và cơ chế giải quyết kết quả. Trong các phiên bản tới, tính năng **tạo market cộng đồng** sẽ được mở rộng.

***

**Điều gì xảy ra nếu sự kiện bị hủy hoặc không thể xác định kết quả?**

PrediX có cơ chế **hoàn tiền (Refund Mode)** cho trường hợp này. Toàn bộ USDC sẽ được hoàn lại cho tất cả người tham gia theo tỷ lệ token đang nắm giữ — không ai đúng, không ai sai, vốn được trả lại đầy đủ.

***

**Tôi có thể nạp USDC từ chain khác không?**

Có. PrediX hỗ trợ **CCTP V2** — cho phép bạn chuyển USDC trực tiếp từ Ethereum, Base, Arbitrum và các chain được hỗ trợ sang Unichain mà không cần qua sàn tập trung.

***

**PrediX có token riêng không?**

Thông tin về token và tokenomics sẽ được công bố chính thức trước khi mainnet ra mắt. Theo dõi kênh **Discord và Twitter** của PrediX để cập nhật sớm nhất.

***

**Tôi có thể cung cấp thanh khoản và kiếm phí không?**

Có. Bạn có thể cung cấp thanh khoản vào bể AMM của từng market và nhận về một phần phí giao dịch từ tất cả người swap qua bể đó. Tính năng này sẽ được hướng dẫn chi tiết trong phần **Liquidity Provider** của docs.

***

_Còn câu hỏi khác? Tham gia cộng đồng PrediX trên **Discord** hoặc đọc thêm tại **docs.predix.io**_
