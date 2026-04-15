# 6. Lớp Đầu Cơ (Speculation Layer)

### 6.1 Tối ưu hoá thanh khoản từ Outcome Tokens

Outcome tokens trên PrediX tuân chuẩn ERC-20, cho phép người nắm giữ cung cấp thanh khoản vào các pool (ví dụ: YES/USDC) trên Uniswap v4 - nhận phí giao dịch (swap fees) trong khi vẫn duy trì vị thế dự đoán.

Nói cách khác, thay vì để outcome tokens nằm yên trong ví chờ sự kiện kết thúc, người dùng có thể đưa chúng vào hoạt động trong hệ sinh thái DeFi. Đây là tính năng mà chưa nền tảng dự đoán lớn nào triển khai được nhờ hạn chế về kiến trúc token.

***

### 6.2 Composability — Lộ trình mở rộng (Phase 3)

Nhờ outcome tokens là ERC-20 chuẩn và vận hành trên Unichain (tương thích EVM), PrediX có thể tích hợp với các protocol DeFi khác trong tương lai. Một số hướng đang được nghiên cứu:

**Lending & Collateral**  : Cho phép sử dụng outcome tokens (YES/NO) làm tài sản thế chấp trong các lending protocol, mở ra khả năng quản lý vốn linh hoạt hơn.

**Vault chiến lược :**  Các vault tổng hợp nhiều vị thế dự đoán khác nhau, giúp người dùng tiếp cận đa dạng thị trường thông qua một điểm vào duy nhất.

**Vị thế đòn bẩy** : Khả năng vay dựa trên outcome tokens đang nắm giữ để mở rộng mức độ tham gia. Tính năng này đi kèm rủi ro thanh lý cao hơn và chỉ phù hợp với người dùng có kinh nghiệm.

**AI Agent SDK** — Bộ công cụ cho phép nhà phát triển xây dựng agent tự động tương tác với thị trường dự đoán: thực thi chiến lược, backtesting, và marketplace cho các mô hình dự đoán.

> **Disclaimer:** Các tính năng trong Phase 3 đang ở giai đoạn nghiên cứu và phát triển, chưa có lịch ra mắt xác định. Kiến trúc cuối cùng có thể thay đổi tuỳ thuộc vào điều kiện kỹ thuật và quy định tại từng thị trường.

