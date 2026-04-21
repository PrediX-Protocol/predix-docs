# 19. Resolution (Kết Quả Market)

## Vòng Đời Của Một Market

Mỗi market dự đoán trải qua các trạng thái sau theo thứ tự:

```
Active → Expired → Resolved | Emergency Resolved | Refund Mode → Redeemed/Refunded → (eventually) Swept
```

Chi tiết state machine: [MarketFacet lifecycle](../smart-contracts/market-facet.md#lifecycle-diagram).

---

## Trường Hợp Bình Thường

Khi market kết thúc và oracle báo cáo kết quả thành công:

- Token của người thắng có giá trị **$1 mỗi token** (trừ redemption fee — xem [§20](20-phi-giao-dich.md))
- Token của người thua có giá trị **$0**
- User vào app và nhấn **"Rút thưởng"** → nhận USDC về ví

**Kỹ thuật**: chuỗi call `oracle.resolve(...)` (by admin nếu manual, hoặc auto trigger nếu Chainlink) → anyone call `Diamond.resolveMarket(marketId)` → user call `Diamond.redeemMarketTokens(marketId)`.

> 💡 Ví dụ: Bạn mua 50 token "YES" với giá $0.60/token, tổng bỏ ra $30. Nếu YES đúng + redemption fee 2% → nhận `50 × ($1 − 0.02) = $49` USDC → lời $19.

---

## Redemption Fee Snapshot (quan trọng)

{% hint style="warning" %}
**Fee được "chụp" tại thời điểm tạo market**. Admin **không thể** raise fee retroactively cho market đã tồn tại. Đây là security guarantee FINAL-H04 từ audit.
{% endhint %}

Khi market được tạo, contract lưu `snapshottedRedemptionFeeBps` = giá trị `defaultRedemptionFeeBps` hiện tại. Thay đổi `defaultRedemptionFeeBps` sau đó CHỈ ảnh hưởng market mới tạo — không ảnh hưởng market đã có.

Admin có thể override per market qua `setPerMarketRedemptionFeeBps(marketId, newBps)`, nhưng chỉ **giảm xuống** (hard cap vẫn `MAX_REDEMPTION_FEE_BPS = 1500` bps = 15%).

---

## Nếu Oracle Không Báo Kết Quả

Đôi khi nguồn dữ liệu bên ngoài gặp sự cố. Trong trường hợp đó:

- Sau **7 ngày** (`EMERGENCY_DELAY`) kể từ khi market hết hạn, **Operator role** có thể gọi `emergencyResolve(marketId, outcome)` để xác nhận kết quả thủ công
- Quá trình redeem sau đó diễn ra bình thường

On-chain: `Diamond.emergencyResolve(marketId, outcome)` với role `OPERATOR_ROLE`. Xem [Access Control](../smart-contracts/33-access-control-safety.md).

---

## Nếu Market Không Thể Giải Quyết Công Bằng

Trong tình huống hiếm — sự kiện bị hủy, dữ liệu mâu thuẫn, không thể xác định — **Operator** kích hoạt **chế độ hoàn tiền** (`refundModeActive`):

- Toàn bộ market chuyển sang trạng thái **refund**
- Bạn nhận lại USDC **tương ứng với số token đang nắm giữ**, bất kể YES hay NO
- Không ai thắng/thua — vốn được hoàn trả

On-chain: `Diamond.enableRefundMode(marketId)` → user gọi `Diamond.refund(marketId, amount)`.

{% hint style="info" %}
**Refund mode là one-way**: một khi active, không thể tắt. Đảm bảo user luôn có path exit.
{% endhint %}

---

## Oracle Re-Verification tại Resolve (FINAL-D-03)

Một chi tiết quan trọng cho auditor: `resolveMarket` **re-check oracle vẫn được approve** tại thời điểm resolve, không chỉ tại create.

Lý do: nếu oracle bị compromise sau tạo market, admin có thể revoke `setApprovedOracle(oracle, false)` → market đó sẽ không thể resolve với oracle đã compromise → admin trigger `enableRefundMode` thay vì chấp nhận kết quả giả.

Chi tiết: [Oracle](../smart-contracts/32-oracle.md#3-oracle-re-verify-at-resolve-final-d-03).

---

## Grace Period + Sweep (365 ngày)

Sau khi market resolved, user có **365 ngày** (`GRACE_PERIOD`) để redeem. Sau đó admin có thể gọi `sweepUnclaimed(marketId)` để chuyển phần collateral chưa claim về fee recipient.

Điều này giúp protocol dọn sạch market cực cũ mà vẫn cho user thời gian đủ để claim.
