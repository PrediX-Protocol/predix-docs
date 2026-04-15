# 24. Smart Routing

### Cách Router Tìm Giá Tốt Nhất Cho Bạn

Khi bạn đặt lệnh, Router không đơn giản chỉ mua/bán ở một nơi duy nhất. Nó **kiểm tra cả hai nguồn thanh khoản** rồi mới quyết định thực thi:

```
Bước 1: Kiểm tra sổ lệnh — có ai đang chờ bán đúng giá bạn muốn không?
Bước 2: Kiểm tra AMM — giá tự động đang ở mức nào?
Bước 3: So sánh và chia lệnh để bạn luôn nhận được giá tốt nhất
```

***

#### 🔀 Ví Dụ Thực Tế

Bạn muốn mua YES với **$100 USDC**:

<table><thead><tr><th width="259">Nguồn</th><th width="138">Số tiền</th><th>Lý do</th></tr></thead><tbody><tr><td>Sổ lệnh (CLOB)</td><td>$60</td><td>Có người đang chờ bán YES ở giá tốt hơn</td></tr><tr><td>Thanh khoản tự động (AMM)</td><td>$40</td><td>Phần còn lại không có lệnh chờ phù hợp</td></tr><tr><td><strong>Tổng</strong></td><td><strong>$100</strong></td><td>Bạn nhận được lượng YES tối đa có thể</td></tr></tbody></table>

> 💡 Toàn bộ quá trình này xảy ra trong **một giao dịch duy nhất** — bạn không cần tự làm gì.

***

### Khi Nào Mỗi Nguồn Chiếm Ưu Thế?

***

#### 📋 Sổ Lệnh (CLOB) tốt hơn khi...

**Market đang sôi động, nhiều người đặt lệnh chờ**

* Có nhiều lệnh giới hạn đang chờ ở vùng giá tốt
* Lệnh của bạn nhỏ, dễ khớp hoàn toàn từ sổ lệnh
* Bạn mua/bán được đúng giá mình muốn, không bị trượt

***

#### 💧 Thanh Khoản Tự Động (AMM) tốt hơn khi...

**Market vắng lệnh hoặc bạn cần mua/bán số lượng lớn gấp**

* Sổ lệnh ít người tham gia, không đủ lệnh để khớp
* Bạn cần fill ngay lập tức, không muốn chờ
* Lệnh quá lớn so với độ sâu của sổ lệnh

***

> ⚡ **Điểm mấu chốt:** Bạn không cần quan tâm đến việc chọn nguồn nào — Router tự động làm điều đó. Nhiệm vụ của bạn chỉ là đặt lệnh và đặt mức slippage hợp lý để bảo vệ bản thân.
