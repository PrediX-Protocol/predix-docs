# 26. Virtual NO Pricing

### Tại Sao Token NO Không Có Bể Thanh Khoản Riêng?

Hệ thống chỉ có một bể thanh khoản duy nhất: **YES/USDC**. Token NO không có bể riêng — nhưng bạn vẫn mua/bán NO bình thường vì hệ thống tự xử lý phần phức tạp phía sau.

***

### Điều Gì Xảy Ra Phía Sau Khi Bạn Giao Dịch NO?

#### Khi bạn **mua NO**:

```
Bạn trả USDC
  → Hệ thống đúc ra một cặp YES + NO
  → Bán phần YES vào bể thanh khoản
  → Giữ lại NO và giao cho bạn
Bạn nhận NO ✅
```

#### Khi bạn **bán NO**:

```
Bạn trả NO
  → Hệ thống mua YES từ bể thanh khoản
  → Ghép YES + NO thành cặp hoàn chỉnh
  → Đổi cặp đó thành USDC
Bạn nhận USDC ✅
```

> 💡 Toàn bộ quá trình trên xảy ra **tức thì trong một giao dịch duy nhất**. Từ góc nhìn của bạn: bỏ USDC vào, nhận NO ra — hoặc ngược lại. Đơn giản như vậy.

***

### Giá YES và NO Liên Quan Thế Nào?

Giá của hai token luôn **cộng lại xấp xỉ $1.00**:

```
Giá NO  ≈  $1.00 − Giá YES

Ví dụ:
  YES đang ở $0.70  →  NO xấp xỉ $0.30
  YES đang ở $0.40  →  NO xấp xỉ $0.60
```

**Điều gì giữ cho tỷ lệ này ổn định?**

Khi giá lệch khỏi cân bằng, các trader chuyên nghiệp (arbitrageur) sẽ ngay lập tức nhảy vào kiếm lợi nhuận từ sự chênh lệch đó — và chính hành động này kéo giá trở về đúng tỷ lệ.

**Ví dụ:** Nếu YES = $0.60 và NO = $0.50 (tổng $1.10 — quá cao):

* Trader mua cặp YES+NO với $1.00
* Bán cả hai ra thị trường thu về $1.10
* Kiếm $0.10 lợi nhuận → áp lực bán kéo giá xuống về $1.00

> ⚡ Đây là cơ chế tự cân bằng tự động — không cần ai quản lý, thị trường tự điều chỉnh.
