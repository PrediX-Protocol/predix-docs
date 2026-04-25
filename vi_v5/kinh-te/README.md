# PRX token & kinh tế

PRX là token quản trị và chia sẻ doanh thu của PrediX. Chưa TGE.

## Đây không phải investment doc

Phần này giải thích **cách token hoạt động** để user quyết định có tham gia không. Không phải pitch đầu tư, không phải hứa ROI.

**Trạng thái hiện tại**:
- Token chưa mint, chưa trade.
- Testnet reward qua **points campaign** — convert sang PRX tại TGE theo tỷ lệ công bố trước.
- TGE dự kiến **Q4 2026**, sau khi mainnet đạt PMF gate ($500k volume/tháng, 1000 trader unique, 10 market active).

## Bốn câu hỏi cơ bản

| Câu hỏi | Câu trả lời ngắn | Đọc thêm |
|---|---|---|
| Token phân bổ ra sao? | 1B total, 35% community, 20% treasury, 18% team, 15% investor, 12% LP/ops | [Phân bổ & vesting](allocation-vesting.md) |
| Token có utility gì? | Stake USDC yield, vote governance, gauge vote LP incentive | [Staking real yield](staking-real-yield.md) |
| Governance thế nào? | Lock PRX → vePRX → vote gauge + protocol params | [vePRX & gauge](veprx-gauge.md) |
| Giá có mechanic gì hỗ trợ? | 30% phí → buyback-burn, deflationary nếu volume đủ | [Buyback-burn](buyback-burn.md) |

## Tại sao real yield thay vì staking inflation

Nhiều protocol pay staker bằng emission (mint token mới). PrediX pay bằng **phí thu thực tế bằng USDC**.

- Staker 1000 PRX không nhận thêm PRX → nhận USDC từ phí protocol chia về.
- Không dilute supply. Emission gần như 0.
- Yield gắn với volume thật — protocol lỗ thì staker nhận ít, không ép phát hành.

Mô hình từ GMX, Pendle, Synthetix (staking revenue model). Khác Aave/Lido (inflationary rewards).

## Phí đi đâu

| Nguồn phí | 50% Staker | 30% Buyback-burn | 20% Treasury |
|---|---|---|---|
| AMM dynamic fee | USDC real | PRX buy & burn | Fund dev + LP subsidy |
| CLOB taker fee | USDC real | PRX buy & burn | ~ |
| Market creation fee | USDC real | PRX buy & burn | ~ |
| Redemption fee | USDC real | PRX buy & burn | ~ |

Break-even protocol: ~$4.5–7M volume/tháng (tuỳ fee tier phân bổ). Tính toán chi tiết [Buyback-burn](buyback-burn.md).

## Cảnh báo

- Không phải tất cả token launch đều đi đúng plan. Đọc [changelog](../tai-nguyen/changelog.md) và announcements để update.
- Tokenomics có thể điều chỉnh trước TGE dựa trên feedback market.
- Đừng all-in. DYOR.
