# 23. Limit Orders

### Đặt Lệnh Giới Hạn (Limit Order)

Khác với lệnh thị trường (khớp ngay), lệnh giới hạn cho phép bạn **chỉ định chính xác mức giá mong muốn** và chờ thị trường đến giá đó mới khớp.

***

#### 📝 Các Thông Tin Cần Điền Khi Đặt Lệnh

<table><thead><tr><th width="155">Thông tin</th><th width="355">Mô tả</th><th>Ví dụ</th></tr></thead><tbody><tr><td><strong>Market</strong></td><td>Bạn đang giao dịch market nào</td><td>"BTC vượt $100k trước Q3?"</td></tr><tr><td><strong>Loại lệnh</strong></td><td>Mua YES / Bán YES / Mua NO / Bán NO</td><td>Mua YES</td></tr><tr><td><strong>Giá</strong></td><td>Mức giá bạn muốn, từ $0.01 đến $0.99</td><td>$0.65</td></tr><tr><td><strong>Số lượng</strong></td><td>Số token muốn mua/bán, tối thiểu 1 token</td><td>50 token</td></tr></tbody></table>

***

#### 📌 Lưu Ý Quan Trọng

**Về giá:** Giá token phản ánh xác suất thị trường đánh giá. Token YES ở giá $0.65 nghĩa là thị trường đang cho sự kiện đó **65% khả năng xảy ra**.

* Giá thấp nhất có thể đặt: **$0.01**
* Giá cao nhất có thể đặt: **$0.99**
* Bước giá tối thiểu: **$0.01** (không thể đặt $0.655 hay $0.657)

**Về số lượng:**

* Tối thiểu mỗi lệnh: **1 token** (tương đương $1)
* Mỗi người được đặt tối đa **50 lệnh đang chờ** trong cùng một market — hãy hủy lệnh cũ trước khi đặt thêm nếu đã gần đến giới hạn

***

### Hủy Lệnh

Bạn có thể hủy lệnh đang chờ khớp bất cứ lúc nào để lấy lại tiền.

**Ai có thể hủy?**

* Trong thời gian market còn mở: **chỉ bạn** mới hủy được lệnh của mình
* Sau khi market hết hạn: **bất kỳ ai** cũng có thể hủy giúp (giúp giải phóng vốn cho người đặt lệnh)

***

### Xem Thông Tin Sổ Lệnh

Trước khi đặt lệnh, bạn có thể xem tình trạng thị trường đang như thế nào:

***

#### 📊 Giá Tốt Nhất Hiện Tại

Xem ngay mức giá mua/bán tốt nhất đang có trên sổ lệnh:

<table><thead><tr><th width="297">Thông tin</th><th>Ý nghĩa</th></tr></thead><tbody><tr><td><strong>Giá mua YES tốt nhất</strong></td><td>Người mua đang trả cao nhất cho token YES</td></tr><tr><td><strong>Giá bán YES tốt nhất</strong></td><td>Người bán đang chào thấp nhất cho token YES</td></tr><tr><td><strong>Giá mua NO tốt nhất</strong></td><td>Người mua đang trả cao nhất cho token NO</td></tr><tr><td><strong>Giá bán NO tốt nhất</strong></td><td>Người bán đang chào thấp nhất cho token NO</td></tr></tbody></table>

> 💡 Chênh lệch giữa giá mua và giá bán càng nhỏ → thị trường càng thanh khoản tốt, bạn giao dịch càng ít bị thiệt.

***

#### 📖 Sổ Lệnh Chi Tiết

Xem toàn bộ các mức giá đang có lệnh chờ, theo chiều sâu bạn muốn (5 cấp, 10 cấp, 20 cấp...). Giúp bạn đánh giá được:

* Thanh khoản đang tập trung ở vùng giá nào
* Đặt lệnh lớn có bị trượt giá nhiều không

***

#### 🔍 Thanh Khoản Tại Một Mức Giá Cụ Thể

Muốn biết ở mức giá $0.60 đang có bao nhiêu token YES đang chờ mua/bán? Dùng tính năng này để kiểm tra chính xác trước khi đặt lệnh lớn.
