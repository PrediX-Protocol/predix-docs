---
description: Hiểu giao diện giao dịch PrediX
---

# Hiểu giao diện

### Bố cục chính <a href="#bo-cuc-chinh" id="bo-cuc-chinh"></a>

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Chú thích: _Price Chart_ = Biểu đồ giá, _Your Position_ = Vị thế của bạn, _Trading Panel_ = Bảng giao dịch, _Order Book_ = Sổ lệnh, _Split / Merge_ = Tách / Gộp, _Market Info_ = Thông tin market.

### Your Position — Vị thế của bạn (trên phải) <a href="#your-position-vi-the-cua-ban-tren-phai" id="your-position-vi-the-cua-ban-tren-phai"></a>

Hiển thị những gì bạn đang nắm giữ trong market này:

```
Your Position
  YES    65 shares     ← bạn sở hữu 65 token YES
  NO     0 shares      ← bạn không sở hữu token NO
```

## Trading Panel — Bảng giao dịch (phải)

#### Nút YES / NO <a href="#nut-yes-no" id="nut-yes-no"></a>

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

* Tô xanh = đang chọn
* Giá hiển thị = giá thị trường hiện tại

#### Chuyển đổi Buy / Sell <a href="#chuyen-doi-buy-sell" id="chuyen-doi-buy-sell"></a>

* **Buy**: chi USDC để nhận shares (cổ phần)
* **Sell**: bán shares lấy USDC

#### Chuyển đổi Market / Limit <a href="#chuyen-doi-market-limit" id="chuyen-doi-market-limit"></a>

* **Market**: mua/bán ngay ở giá hiện tại
* **Limit**: đặt giá riêng, chờ khớp

#### Nhập số tiền (Amount) <a href="#nhap-so-tien-amount" id="nhap-so-tien-amount"></a>

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

* Nhập số tiền hoặc nhấn nút preset
* **MAX** = toàn bộ số dư
* **Balance** = số dư hiện có (để tham khảo)

#### Nút thực hiện <a href="#nut-thuc-hien" id="nut-thuc-hien"></a>

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Hiển thị hành động và giá.

## Price Chart — Biểu đồ giá (trái)

Hiển thị lịch sử giá theo thời gian:

* Đường xanh = giá tăng
* Đường đỏ = giá giảm
* Di chuột để xem giá tại thời điểm cụ thể

## Order Book — Sổ lệnh (dưới trái)

```
Price    Qty        Total
$0.40    61.58      543.73    ← Asks (sellers)
$0.39    69.68      482.15
$0.38    70.34      412.47

         $0.38   Spread: $0.03      ← Mid price

$0.35    24.67      24.67     ← Bids (buyers)
$0.34    31.87      56.54
$0.33    31.29      87.83
```

* **Đỏ (trên)** = Asks — người muốn bán ở giá này
* **Xanh (dưới)** = Bids — người muốn mua ở giá này
* **Spread** = khoảng cách giữa giá bid tốt nhất và ask tốt nhất

## Split / Merge — Tách / Gộp

```
┌──────────────────────────────┐
│  [Split]        [Merge]      │
│                              │
│  Split USDC into equal       │
│  YES + NO tokens             │
│                              │
│  ┌────────────────────┐      │
│  │ 100.00        USDC │      │
│  └────────────────────┘      │
│                              │
│  ┌────────────────────┐      │
│  │       Split         │      │
│  └────────────────────┘      │
└──────────────────────────────┘
```

* **Split (Tách)**: $100 USDC → 100 YES + 100 NO token
* **Merge (Gộp)**: 100 YES + 100 NO → $100 USDC
* Hữu ích cho các chiến lược nâng cao

## Market Info — Thông tin market

```
Market Info
  Category:          crypto
  Total Collateral:  $9,936.67 USDC
  Status:            active
  Oracle:            0x699a8c...992cf3
```

* **Total Collateral**: tổng USDC bị khóa trong market
* **Status**: active / ended / resolved
* **Oracle**: ai xác định kết quả

***
