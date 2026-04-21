# 18. Split & Merge & Events

## Split: $1 USDC → 1 YES + 1 NO

Gửi USDC vào Diamond contract. Nhận lại 1 YES token + 1 NO token. USDC được giữ làm collateral. Tổng cung `YES.totalSupply = NO.totalSupply = totalCollateral` luôn được bảo toàn (invariant INV-1).

Function: `Diamond.splitPosition(marketId, amount)`. Xem [MarketFacet reference](../smart-contracts/market-facet.md#splitposition).

## Merge: 1 YES + 1 NO → $1 USDC

Trả 1 YES + 1 NO cho Diamond. Nhận lại $1 USDC. Tokens bị burn. Tổng cung giảm tương ứng.

Function: `Diamond.mergePositions(marketId, amount)`.

## Tại Sao Quan Trọng?

- **Neo giá**: YES + NO ≈ $1 nhờ arbitrage — bất kỳ ai cũng có thể split $1 → cặp token rồi bán cả hai nếu tổng > $1 (hoặc mua cả hai + merge nếu tổng < $1).
- **Tạo thanh khoản**: LP cần YES hoặc NO tokens → split trước để có inventory.
- **Chiến lược**: Mua "NO" bằng cách split + bán YES (hoặc ngược lại). Đây là mechanism hoạt động của Virtual NO Pricing trong Router. Xem [Virtual NO Pricing](../giao-dich/26-virtual-no-pricing.md).

## Multi-Outcome Events (EventFacet)

{% hint style="info" %}
Trước đây tính năng này được gọi là "Category" trong docs cũ. Canonical on-chain là **Event** / **EventFacet**.
{% endhint %}

Event là một **nhóm N binary market liên kết** với invariant mutual exclusion — chỉ 1 market YES-wins, còn lại NO-wins.

Use case phổ biến:

- **Bầu cử nhiều ứng viên**: 1 event "Ai thắng bầu cử Tổng thống Mỹ 2028?" với N markets (mỗi candidate 1 market).
- **Sự kiện thể thao**: "Ai vô địch World Cup 2026?" với N market cho mỗi đội.
- **Xếp hạng**: "Top 1 album Billboard 2027?" với N market.

### User flow ngắn

1. Admin tạo event với N market con qua `EventFacet.createEvent(name, childQuestions[], endTime, oracle)`.
2. User chọn 1 (hoặc nhiều) market con để bet → split/trade như binary thường.
3. Oracle resolve event → chỉ 1 market YES-wins, các market khác NO-wins tự động.
4. User holding YES của winning market → redeem $1/token. User holding YES của losing markets → 0.

### Tại sao không dùng "group split"?

Docs cũ có nhắc `GroupSplit` (split USDC đồng thời cho tất cả outcomes trong category) — tính năng này **chưa có** trong source code hiện tại. Nếu cần rủi ro spread, hiện tại user split thủ công từng market con.

Chi tiết on-chain: [EventFacet reference](../smart-contracts/event-facet.md).
