# Redeem & refund

Sau khi market resolve, bạn có thể đổi token thắng thành USDC. Nếu không resolve được, dùng refund.

## Redeem — market đã resolve

**Điều kiện**:
- `market.isResolved == true`.
- Bạn giữ token đúng phía (YES nếu outcome=true, NO nếu outcome=false).

### Bước

1. Vào [Portfolio](portfolio.md), filter **Resolved markets**.
2. Mỗi market có nút **Redeem** nếu bạn có token thắng.
3. Click → preview: số token, fee redemption, USDC nhận.
4. Confirm ví → ~2s tx complete.
5. USDC vào ví. Token thắng bị burn, token thua = $0 (không redeem được).

### Batch redeem

Nếu có nhiều market đã resolve, Portfolio có nút **Claim all** → batch qua smart account (1 click, 1 tx).

### Công thức

```
payout = winningAmount × (10000 − redemptionFeeBps) / 10000
```

Ví dụ: 205 YES đúng, fee 1% (100 bps):

```
payout = 205 × 9900 / 10000 = 202.95 USDC
fee   = 205 − 202.95        =   2.05 USDC
```

`redemptionFeeBps` snapshot tại thời điểm tạo market, không đổi retroactively. Xem [Cấu trúc phí](../khai-niem/phi.md).

### Token thua (NO khi YES thắng)

- **Không còn giá trị**.
- Không redeem được, không trade được (pool drained).
- Trong ví vẫn thấy balance nhưng market value = $0. Dọn bằng cách hide.

## Refund mode

Khi market không thể resolve (oracle down, dispute không xử lý được):

**Điều kiện**:
- `market.refundModeActive == true` (admin bật qua timelock).
- Bạn giữ **cả YES và NO**.

### Bước

1. Portfolio → market hiển thị badge **Refund available**.
2. Click **Refund** → preview: `min(yesBalance, noBalance) = 80`, nhận 80 USDC.
3. Confirm → tx ~2s.
4. USDC về ví. Token YES+NO cặp bị burn.

### Công thức refund

```
refundAmount = min(yesBalance, noBalance)
payout       = refundAmount USDC
```

Ví dụ: bạn có 100 YES + 80 NO:
- Refund được 80 cặp → nhận 80 USDC.
- Còn dư 20 YES → **không có NO để pair**. Không refund được.

**Cảnh báo quan trọng**: Refund theo cặp. Nếu bạn chỉ có 1 phía (ví dụ mua 100 YES qua Router, không giữ NO), bạn không claim lại được USDC qua refund flow. Cách duy nhất: mua NO từ ai đó (nếu còn ai bán) để cặp với YES, rồi refund.

### Đây là trade-off

Refund mode ưu tiên **pro-rata công bằng** thay vì first-come-first-serve. Tránh trường hợp ai nhanh tay claim trước được, người sau không còn USDC.

Đã có thảo luận mở rộng refund mode cho phép single-sided với burn & haircut, nhưng chưa implement. Theo dõi [changelog](../tai-nguyen/changelog.md).

## Ai quyết định enable refund

- **Admin multisig** + **TimelockController 48h**. Không thể instant.
- Đủ 48h delay để community challenge nếu sai. Sau 48h tự động executable.
- Event `RefundModeEnabled` emit on-chain, Discord + Twitter thông báo trước.

## Timing

- **Redemption window**: vô thời hạn sau resolve, nhưng có **grace 365 ngày**.
- **Sau 365 ngày**: Admin có thể `sweepUnclaimed` — thu hồi token chưa claim về treasury.
- Claim trong 1 năm để khỏi quên.

## Lỗi thường gặp

**"Market not resolved"** — Market qua endTime nhưng oracle chưa call. Chờ.

**"No winning tokens"** — Bạn có token thua (YES khi outcome=false). Không redeem được.

**"Refund not active"** — Market đang waiting resolve hoặc đã resolved. Không phải mọi market đều refund — chỉ khi oracle fail.

**"Unequal YES/NO balance"** — Refund chỉ theo cặp. Giải quyết: trade để cân bằng số lượng, hoặc accept loss phần không pair được.
