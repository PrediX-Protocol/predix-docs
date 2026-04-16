# Lớp Đầu Cơ (Speculation Layer)

### 1 Tối ưu hoá thanh khoản từ Outcome Tokens

Outcome tokens trên PrediX tuân chuẩn ERC-20, cho phép người nắm giữ cung cấp thanh khoản vào các pool (ví dụ: YES/USDC) trên Uniswap v4 - nhận phí giao dịch (swap fees) trong khi vẫn duy trì vị thế dự đoán.

Nói cách khác, thay vì để outcome tokens nằm yên trong ví chờ sự kiện kết thúc, người dùng có thể đưa chúng vào hoạt động trong hệ sinh thái DeFi. Đây là tính năng mà chưa nền tảng dự đoán lớn nào triển khai được nhờ hạn chế về kiến trúc token.

***

### 2 Composability — Lộ trình mở rộng (Phase 3)

Nhờ outcome tokens là ERC-20 chuẩn và vận hành trên Unichain (tương thích EVM), PrediX có thể tích hợp với các protocol DeFi khác trong tương lai. Một số hướng đang được nghiên cứu:

**Lending & Collateral**  : Cho phép sử dụng outcome tokens (YES/NO) làm tài sản thế chấp trong các lending protocol, mở ra khả năng quản lý vốn linh hoạt hơn.

**Vault chiến lược :**  Các vault tổng hợp nhiều vị thế dự đoán khác nhau, giúp người dùng tiếp cận đa dạng thị trường thông qua một điểm vào duy nhất.

**Vị thế đòn bẩy** : Khả năng vay dựa trên outcome tokens đang nắm giữ để mở rộng mức độ tham gia. Tính năng này đi kèm rủi ro thanh lý cao hơn và chỉ phù hợp với người dùng có kinh nghiệm.

**AI Agent SDK** — Bộ công cụ cho phép nhà phát triển xây dựng agent tự động tương tác với thị trường dự đoán: thực thi chiến lược, backtesting, và marketplace cho các mô hình dự đoán.

> **Disclaimer:** Các tính năng trong Phase 3 đang ở giai đoạn nghiên cứu và phát triển, chưa có lịch ra mắt xác định. Kiến trúc cuối cùng có thể thay đổi tuỳ thuộc vào điều kiện kỹ thuật và quy định tại từng thị trường.

#### 6.1 Kiếm Lãi Từ Vị Thế Dự Đoán

Thông thường, khi bạn mua token YES hoặc NO trên một prediction market, số vốn đó "nằm yên" — bạn chỉ có thể thắng hoặc thua khi sự kiện kết thúc. Không hơn không kém.

**PrediX thay đổi điều này.**

Vì outcome token YES/NO là ERC-20 chuẩn, bạn có thể đem token YES vào pool thanh khoản YES/USDC trên Uniswap v4 — tức là vừa **giữ nguyên cược dự đoán**, vừa **thu phí swap** từ những người giao dịch trong pool đó.

> Nói đơn giản: vốn của bạn không còn "ngủ" trong khi chờ kết quả — nó tiếp tục sinh lời mỗi ngày.

Đây là điểm mà **không một nền tảng prediction market lớn nào** (Polymarket, Kalshi…) có thể làm được, vì họ không xây trên AMM với outcome token chuẩn ERC-20.

***

#### 6.2 Khả Năng Mở Rộng Trong Tương Lai (Phase 3)

Khi nền tảng trưởng thành, PrediX sẽ mở khóa thêm các lớp tài chính phức tạp hơn:

<table><thead><tr><th width="274">Tính năng</th><th>Ý nghĩa thực tế</th></tr></thead><tbody><tr><td><strong>Lending với outcome token</strong></td><td>Dùng token YES/NO làm tài sản thế chấp để vay USDC — giải phóng thanh khoản mà không cần bán cược</td></tr><tr><td><strong>Structured Vault</strong></td><td>Vault tự động kết hợp nhiều vị thế dự đoán thành một sản phẩm đầu tư đóng gói sẵn</td></tr><tr><td><strong>Dự đoán đòn bẩy</strong></td><td>Vay thêm vốn đối ứng với vị thế hiện có để khuếch đại lợi nhuận (và rủi ro)</td></tr><tr><td><strong>AI Agent SDK</strong></td><td>Bộ công cụ cho phép bot giao dịch tự động, marketplace chiến lược, và engine</td></tr></tbody></table>
