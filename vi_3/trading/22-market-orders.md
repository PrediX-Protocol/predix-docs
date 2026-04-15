# 22. Market Orders

### Các Lệnh Giao Dịch Cơ Bản

***

#### 🟢 Mua YES — Bạn tin sự kiện sẽ xảy ra

Bỏ vào USDC, nhận về token YES.

| Bạn cung cấp        | Bạn nhận về |
| ------------------- | ----------- |
| Số USDC muốn bỏ vào | Token YES   |

***

#### 🔴 Mua NO — Bạn tin sự kiện sẽ KHÔNG xảy ra

Mua NO phức tạp hơn một chút vì hệ thống phải xử lý thêm bước trung gian:

1. USDC của bạn được dùng để đúc ra một cặp **YES + NO**
2. Phần YES được **bán ngay** trên thị trường
3. Bạn **giữ lại** phần NO

> 💡 Bạn không cần làm gì thêm — toàn bộ quá trình trên xảy ra tự động trong một giao dịch duy nhất.

***

#### 💰 Bán YES — Chốt lời hoặc cắt lỗ vị thế YES

Trả lại token YES, nhận về USDC.

| Bạn cung cấp          | Bạn nhận về |
| --------------------- | ----------- |
| Số token YES muốn bán | USDC        |

***

#### 💰 Bán NO — Chốt lời hoặc cắt lỗ vị thế NO

Bán NO cũng có bước trung gian ngược lại với mua NO:

1. Hệ thống **mua YES** trên thị trường với lượng tương ứng token NO của bạn
2. Ghép YES + NO thành cặp hoàn chỉnh
3. Đổi cặp đó thành **USDC** trả về cho bạn

> 💡 Tương tự, toàn bộ xảy ra tự động — bạn chỉ thấy NO vào, USDC ra.

***

### Hai Tính Năng Bảo Vệ Bạn Khi Giao Dịch

***

#### 🛡️ Bảo Vệ Trượt Giá (Slippage Protection)

Thị trường có thể biến động trong lúc lệnh của bạn đang được xử lý. Để tránh mua/bán ở giá quá xấu, bạn luôn đặt một **mức tối thiểu chấp nhận được**:

* Mua: "Tôi bỏ X USDC, nhưng phải nhận được **ít nhất Y token** — không thì hủy"
* Bán: "Tôi bán X token, nhưng phải nhận về **ít nhất Y USDC** — không thì hủy"

Nếu thị trường trượt quá mức đó → giao dịch tự động **bị hủy**, tiền không mất.

***

#### ⏱️ Thời Hạn Giao Dịch (Deadline)

Mỗi lệnh có một **thời hạn hiệu lực** (thường là 5 phút). Nếu vì lý do nào đó lệnh chưa được xác nhận trên blockchain trong khoảng thời gian này → giao dịch tự động **bị hủy**.

Điều này tránh tình trạng lệnh cũ bị thực thi muộn khi giá đã thay đổi hoàn toàn.

***

### Xem Trước Giá Trước Khi Giao Dịch

Trước khi thực sự đặt lệnh, bạn có thể **xem trước kết quả ước tính** mà không tốn phí:

<table><thead><tr><th width="338">Bạn muốn biết</th><th width="168">Nhập vào</th><th>Kết quả trả về</th></tr></thead><tbody><tr><td>Mua YES với X USDC được bao nhiêu?</td><td>Số USDC</td><td>Số YES ước tính</td></tr><tr><td>Bán Y token YES được bao nhiêu USDC?</td><td>Số YES</td><td>Số USDC ước tính</td></tr><tr><td>Mua NO với X USDC được bao nhiêu?</td><td>Số USDC</td><td>Số NO ước tính</td></tr><tr><td>Bán Y token NO được bao nhiêu USDC?</td><td>Số NO</td><td>Số USDC ước tính</td></tr></tbody></table>

> 💡 **Luôn dùng tính năng này trước khi giao dịch** — giúp bạn biết chính xác mình sẽ nhận được gì và từ đó đặt mức slippage protection hợp lý.
