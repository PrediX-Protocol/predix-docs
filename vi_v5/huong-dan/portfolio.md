# Portfolio & P\&L

Xem tất cả vị thế, lịch sử, P\&L tại `/portfolio`.

## Màn hình tổng

- **Hero KPIs**: Total value (spot price × balance), Realized P\&L, Unrealized P\&L, Win rate, Accuracy score.
- **Active positions**: Mỗi market bạn đang giữ YES hoặc NO.
- **Open orders**: Lệnh limit đang chờ trên CLOB.
- **History**: Mọi tx (trades, orders, splits, merges, claims, refunds).
- **Calibration**: Brier score + biểu đồ accuracy band (nâng cao).

## Active positions

Mỗi row:

| Market | Side | Balance | Avg cost | Spot price | Unrealized P\&L | Action |
|---|---|---|---|---|---|---|
| BTC > $100k trước 2027 | YES | 205.3 | $0.483 | $0.62 | +$28.20 | Sell / Redeem |
| ETH > $5k trong 2026 | NO | 50.0 | $0.38 | $0.42 | +$2.00 | Sell |

Giá trị dựa trên:
- **Balance**: số YES/NO từ indexer (bảng `position`).
- **Avg cost**: bình quân gia quyền chi phí mua — hydrate từ `position.totalSpent / position.yesBalance`.
- **Spot price**: giá AMM v4 + best bid/ask CLOB (pricing layer).

## Unrealized vs Realized P\&L

- **Unrealized** = (spot price - avg cost) × balance. Chưa chốt lời/lỗ.
- **Realized** = P\&L đã đóng vị thế hoặc redeem. Từ bảng `position.realizedPnl` + redemption events.

Ví dụ:
- Bạn mua 100 YES @ $0.50 = chi 50 USDC.
- Bán 40 YES @ $0.60 = nhận 24 USDC.
- **Realized**: `40 * (0.60 - 0.50) = +4 USDC`.
- **Còn lại**: 60 YES, cost basis $0.50. Spot $0.55.
- **Unrealized**: `60 * (0.55 - 0.50) = +3 USDC`.

## History — 5 loại

| Type | Ý nghĩa | Source event |
|---|---|---|
| Trade | Buy/sell qua Router | `PrediXRouter.Trade` |
| Order | Place/cancel/fill limit order | `PrediXExchange.OrderPlaced / OrderMatched / OrderCancelled` |
| Split | Mint cặp YES+NO | `MarketFacet.PositionSplit` |
| Merge | Burn YES+NO → USDC | `MarketFacet.PositionMerged` |
| Claim | Redeem sau resolve hoặc refund | `MarketFacet.TokensRedeemed / MarketRefunded` |

Mỗi row click được để xem tx hash trên explorer.

## Calibration

Đo độ chính xác dự đoán của bạn trên các market đã resolve.

**Brier score**: trung bình bình phương sai lệch giữa giá bạn mua và outcome thực tế.

- Bạn mua YES @ $0.70 → sự kiện xảy ra (outcome=1) → Brier = `(1 - 0.7)² = 0.09`.
- Bạn mua NO @ $0.30 → sự kiện xảy ra → Brier = `(0 - 0.3)² = 0.09`.
- Score thấp = bạn pricing đúng. Score cao = bạn đoán sai thường xuyên.

**Accuracy band**: biểu đồ kết quả theo khoảng giá mua:
- Mua $0.50–0.60 → 55% sự kiện thực xảy ra (trung bình).
- Nếu band của bạn khớp diagonal (mua $0.7 → 70% win) → bạn well-calibrated.
- Band lệch xuống → bạn tự tin quá, tức là thua nhiều hơn giá implied.

## Streak & reward

- **Win streak**: số market thắng liên tiếp.
- **Badges**: đạt milestone → mở badge NFT (testnet-only, mainnet sẽ có PRX reward).
- **Points**: tracked testnet, convert ratio công bố pre-TGE.

## Export & API

- Tab **Export** → download CSV tất cả history của ví.
- Developer lấy trực tiếp từ Indexer: `GET /api/users/:address/portfolio`.
  Xem [developers/indexer-api.md](../developers/indexer-api.md).

## Hiển thị lên Unrealized sau endTime

Market qua endTime nhưng chưa resolve:
- Trading đóng, không bán được.
- Unrealized = cost basis × balance (hold tới resolve).
- UI đánh badge **Pending resolve**.
- Sau resolve → YES đúng $1, NO đúng $0 → P\&L khép.
