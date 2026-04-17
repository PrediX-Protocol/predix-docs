# 25. Matching Engine

### Bốn Cách Lệnh Được Khớp Trong Sổ Lệnh

***

#### 🤝 Khớp Trực Tiếp — Người Mua Gặp Người Bán

Trường hợp đơn giản nhất: có người muốn mua YES, có người muốn bán YES → hai bên trao đổi thẳng với nhau.

```
Người mua YES  →  trả USDC  →  nhận token YES
Người bán YES  →  trả token YES  →  nhận USDC
```

***

#### 🪙 Đúc Token Mới — Hai Người Cùng Mua Hai Phía

Xảy ra khi có người mua YES **và** người mua NO cùng lúc, với tổng giá **bằng hoặc lớn hơn $1.00**.

Protocol gom USDC của cả hai, đúc ra cặp YES + NO mới, phát cho mỗi người phần token họ muốn.

**Ví dụ:**

* Người A mua YES ở giá $0.72
* Người B mua NO ở giá $0.30
* Tổng: $1.02 → Protocol đúc token, phần $0.02 dư là phí giao thức

> 💡 Cơ chế này giúp tạo ra thanh khoản mới khi thị trường chưa có sẵn token để giao dịch.

***

#### 🔥 Huỷ Token Cũ — Hai Người Cùng Bán Hai Phía

Ngược lại với đúc token: khi có người bán YES **và** người bán NO cùng lúc, với tổng giá **bằng hoặc nhỏ hơn $1.00**.

Protocol gom cặp YES + NO lại, huỷ chúng đi và trả USDC về cho cả hai.

**Ví dụ:**

* Người A bán YES ở giá $0.50
* Người B bán NO ở giá $0.45
* Tổng: $0.95 → Mỗi người nhận lại USDC theo giá họ đặt, phần $0.05 thiếu là chi phí thoát lệnh

> 💡 Cơ chế này cho phép người dùng thoát vị thế ngay cả khi không có người mua trực tiếp ở phía đối diện.

***

#### 🎁 Khớp Giá Tốt Hơn Bạn Đặt

Hệ thống luôn ưu tiên khớp lệnh của bạn ở **mức giá có lợi nhất có thể**, không nhất thiết phải đúng mức giá bạn đặt.

**Ví dụ:**

* Bạn đặt lệnh mua YES ở giá $0.70 (tức là chấp nhận trả tối đa $0.70)
* Thị trường đang có người bán YES ở giá $0.50
* Hệ thống khớp tại $0.50 → **hoàn lại $0.20** cho bạn ngay lập tức

```
Bạn sẵn sàng trả:   $0.70
Thực tế khớp tại:   $0.50
Bạn được hoàn lại:  $0.20  ✅
```

> ⚡ Điều này có nghĩa là đặt lệnh giới hạn không bao giờ khiến bạn trả đắt hơn mức bạn đồng ý — chỉ có thể rẻ hơn.
