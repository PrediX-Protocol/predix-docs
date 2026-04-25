# Event đa outcome

Sự kiện có nhiều lựa chọn: *"Ai thắng FIFA World Cup 2026?"* — mỗi đội là 1 market con, chỉ 1 thắng.

## Cấu trúc

Event = container cho N market con (gọi là **members**). Mỗi member vẫn là market YES/NO bình thường, nhưng thuộc tính:

- Chia sẻ **endTime** chung.
- **Mutual exclusive** — khi event resolve, đúng 1 member YES = true, tất cả còn lại YES = false.
- Có thể **groupSplit / groupMerge** — mint/burn cặp YES của tất cả members cùng lúc.

## Ví dụ

Event: "*FIFA WC 2026 winner*" với 8 members (giả sử đơn giản):
- Argentina, Brazil, France, Germany, England, Spain, Portugal, Netherlands.

Mỗi member là một market YES/NO:
- "Argentina thắng?" → YES = Argentina thắng, NO = Argentina không thắng.
- Tổng giá YES của 8 member ≈ $1 (xác suất cộng lại = 100%).

Khi event resolve (ví dụ Argentina thắng):
- Argentina YES = $1, NO = $0.
- 7 đội còn lại: YES = $0, NO = $1.

## Giao dịch

### Buy YES 1 đội

- UI event: hiện 8 đội cùng giá YES.
- Click Argentina → panel bên phải cho buy YES Argentina như market thường.
- Flow: Router swap USDC → YES Argentina qua CLOB + AMM.

### Buy NO (short 1 đội)

- Tương tự, buy NO Argentina = bet Argentina **không** thắng.
- Payout nếu Argentina không thắng (7 outcome còn lại): NO = $1.

### groupSplit

Action đặc biệt chỉ có ở event:

- Deposit 1 USDC → nhận 1 YES Argentina + 1 YES Brazil + ... + 1 YES Netherlands (tất cả 8 members).
- Atomic — 1 tx mint toàn bộ.
- Thực chất là bet "có đội nào đó thắng" → chắc chắn đúng 1 token đổi $1 khi resolve.

Lúc nào dùng: hedge, hoặc market-make nhiều đội cùng lúc.

### groupMerge

Ngược lại: nếu bạn có đủ 1 YES của tất cả 8 members → burn gộp → nhận 1 USDC.

## Tại sao thiết kế vậy

Event tạo một **constraint** on-chain: exactly 1 member resolve YES = true. Đảm bảo:

- Tổng payout YES = collateral deposited — không ai mất, không ai thắng "nhầm".
- Arbitrage cho phép giá YES members tổng về $1 đúng nghĩa xác suất.
- User trade 1 member riêng không cần biết tổng event — chỉ cần xem giá đội đó.

## Resolution

Oracle event resolve khác resolve market đơn:

- `EventFacet.resolveEvent(eventId, winningIndex)` — 1 tx set toàn bộ.
- `winningIndex` ∈ [0, N-1] — chỉ đúng 1 member được marked YES.
- Atomic: tất cả members resolve cùng block. Không có trường hợp 2 member cùng YES.

## Refund event

Nếu event fail (ví dụ giải đấu bị huỷ):

- Admin `enableEventRefundMode(eventId)` → tất cả members enter refund mode.
- User groupMerge (nếu có đủ YES của mọi member) → nhận USDC pro-rata.
- Hoặc refund từng member riêng lẻ (nếu chỉ có 1 vài).

## Ví dụ UI

Trang [Events](https://app.predix.app/events):

- Grid hiển thị events với tổng volume + deadline.
- Click event → trang detail:
  - **Chart combined**: giá YES của tất cả members trên cùng timeline.
  - **Members table**: mỗi đội 1 row, giá YES, volume, button Buy.
  - **Right panel**: panel trading cho member bạn chọn (click row để swap).

URL sync: `/events/42?outcome=3` → pre-select member index 3.

## Giới hạn

- Max **50 members** / event (gas + UI).
- Không hỗ trợ nested event (event trong event).
- Không hỗ trợ weighted outcome (member này 30% payout, member kia 70%) — chỉ winner-takes-all.

## Khác multi-binary

Đừng nhầm event với 2 market binary riêng biệt:

- *"Argentina thắng?"* + *"Brazil thắng?"* riêng biệt → không enforce tổng ≈ $1. Arbitrage không tự động.
- Event với Argentina + Brazil là members → enforce cơ học. User trade 1 đội, arbitrage lan toả cả event.

Event dùng khi có constraint mutual-exclusive. Market binary dùng khi sự kiện độc lập.
