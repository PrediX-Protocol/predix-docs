# 7. Tokenomics
Tổng cung: 1.000.000.000 PREDIX | Giá khởi điểm: $0,015 | FDV tại TGE: $15M

## 7.1 Phân Bổ Token

| Nhóm | % | Tokens | Vesting |
|---|---|---|---|
| Community & Ecosystem | 35% | 350M | Emission giảm dần 48 tháng |
| Treasury / DAO | 20% | 200M | DAO kiểm soát, 4 năm |
| Team & Advisors | 18% | 180M | 12M cliff + 36M linear (48M tổng) |
| Pre-Seed Investors | 8% | 80M | 6M cliff + 18M linear, TGE 10% |
| Seed Strategic | 7% | 70M | 3M cliff + 15M linear, TGE 10% |
| DEX/CEX Liquidity | 7% | 70M | 50% TGE, 50% trong 12 tháng |
| Protocol Liquidity Seeding | 5% | 50M | Theo milestone mainnet |

50% tổng cung cho community/ecosystem. Vesting team 48 tháng -dài hơn tiêu chuẩn ngành 24 tháng.

## 7.2 Bảy Tiện Ích Token (Token Utility)

### 7.2.1 Staking & Chia Sẻ Doanh Thu
Cơ chế: Stake PREDIX → nhận 50% tổng phí giao dịch protocol bằng USDC
Real yield: Doanh thu từ phí thật, không phải token lạm phát. APY scale tuyến tính theo volume
Ví dụ: $500K volume/tháng × 1% phí trung bình = $5K USDC/tháng cho pool staker
Activation: P0 -từ TGE
Ref: dYdX V4 (100% phí → staker), GMX (30% phí → staker GLP). PrediX chọn mô hình 50/30/20 cân bằng giữa staker, buyback-burn và treasury

### 7.2.2 Oracle & Giải Quyết Tranh Chấp
Cơ chế: Stake PREDIX để vote kết quả tranh chấp qua commit-reveal voting (hash trước, reveal sau để chống front-running)
Incentive: Vote đúng → nhận thưởng từ dispute bond. Vote sai → bị slash theo tỷ lệ
Escalation: Tranh chấp vượt 10% vốn hoá PREDIX → chuyển lên multi-sig
Price markets: Tự động resolve qua Chainlink feeds (VD: “BTC > $200K”)
Activation: P1 -từ mainnet
Ref: UMA DVM (Optimistic Oracle + bond mechanism). PrediX mở rộng bằng commit-reveal thay vì optimistic window

### 7.2.3 Gauge Voting (vePREDIX)
Cơ chế: Khoá PREDIX → nhận vePREDIX (khoá 1–12 tháng, khoá lâu = nhiều voting power hơn)
Tác dụng: vePREDIX holders vote hàng tuần market nào nhận trợ cấp LP từ treasury
Flywheel: Market được vote nhiều → thanh khoản sâu → volume cao → phí nhiều → yield staker tăng → demand PREDIX tăng
Differentiator: “Curve Wars” cho prediction markets -không đối thủ nào có cơ chế này
Activation: P0 -từ TGE
Ref: Curve veCRV (khoá CRV → vote gauge → $4B TVL locked). Balancer veBAL. PrediX áp dụng mô hình đã proven cho vertical mới

### 7.2.4 Giảm Phí Theo Tầng
Cơ chế: Stake PREDIX đạt ngưỡng → giảm phí giao dịch 20–50%
Điều kiện giữ tầng: Kết hợp với volume giao dịch tối thiểu hàng tháng
Tầng (dự kiến): Bronze (giảm 10%), Silver (giảm 20%), Gold (giảm 35%), Diamond (giảm 50%)
Activation: P0 -từ TGE
Ref: Hyperliquid (fee tiers theo volume + staking). BNB fee discount trên Binance. PrediX kết hợp staking + volume thay vì chỉ volume

### 7.2.5 Buyback-Burn
Cơ chế: 30% doanh thu protocol → buyback PREDIX tự động trên thị trường mở → burn vĩnh viễn. On-chain verifiable
Liên kết trực tiếp: Sử dụng nền tảng tăng → burn rate tăng → cung giảm → price floor tự nhiên
Ước tính: 1–3% tổng cung bị burn mỗi năm tại quy mô dự phóng
Activation: P0 -từ TGE (từ ngày đầu có volume)
Ref: Hyperliquid (97% phí → buyback = $49B FDV, $716M burn năm 2025). BNB quarterly burn. PrediX dùng 30% (thay vì 97% như Hyperliquid) để cân bằng với staker yield

### 7.2.6 Bond Tạo Market
Cơ chế: Stake 10.000 PREDIX để tạo market không cần phép (permissionless)
Incentive: Resolve hợp lệ → hoàn bond + thưởng (5–10% phí market). Spam/thao túng → slash toàn bộ bond
Tác dụng: Bộ lọc chất lượng + tạo demand cấu trúc cho PREDIX khi số lượng market scale
Activation: P1 -M+3 sau mainnet
Ref: Azuro (pool creation bond). Hyperliquid HIP-3 (listing bond). PrediX kết hợp bond + revenue share cho market creator

### 7.2.7 Collateral Yield Sharing
Cơ chế: vePREDIX holders nhận phần chia từ yield trên idle USDC collateral (70% USDC protocol → Aave v3 ~4% APY)
Tác dụng: Tạo thêm nguồn real yield ngoài phí giao dịch, scale theo TVL thay vì volume
Ví dụ: Tại TVL $50M → ~$1,4M yield/năm → chia cho vePREDIX holders
Activation: P2 -M+12 sau TGE
Ref: GMX (GLP yield sharing -70% phí cho staker). Polymarket giữ $416M USDC nhàn rỗi ở 0% yield -PrediX biến thành nguồn doanh thu

## 7.2.8 Activation Priority Roadmap

| Utility | Priority | Timeline | Impact | Benchmark |
|---|---|---|---|---|
| Fee Buyback-Burn | P0 | Từ TGE | CAO  - price floor | Hyperliquid: 97% fee buyback |
| veToken Staking (Gauge) | P0 | Từ TGE | CAO  - giảm cung lưu hành | Curve: veCRV model |
| LP Yield Boost | P0 | Từ TGE | CAO  - giữ chân LP | Curve/Balancer gauge |
| Giảm phí theo tầng | P0 | Từ TGE | CAO  - incentive trade | Hyperliquid fee tiers |
| Market Creation Bond | P1 | M+3 sau mainnet | TB  - scale demand | Azuro: pool creation |
| Oracle Dispute Bond | P1 | Từ mainnet | TB  - an toàn hệ thống | UMA: bond mechanism |
| NFT Pass Access | P2 | M+6 sau TGE | TB  - gamification | Blur: NFT + token model |
| Collateral Yield Sharing | P2 | M+12 sau TGE | CAO dài hạn | GMX: GLP yield sharing |


## 7.3 Phân Bổ Doanh Thu

| Phân bổ | % | Cơ chế |
|---|---|---|
| PREDIX Staker | 50% | USDC real yield |
| Buyback-Burn | 30% | Mua → burn vĩnh viễn |
| Treasury | 20% | Vận hành + trợ cấp LP gauge |


## 7.4 Cơ Chế Giảm Phát
Giảm cung: Buyback-burn (1–3% cung/năm), oracle slash, bond slash
Tăng cung: Oracle rewards ~0,5%/năm, staking emission gần 0
Kết quả ròng: Giảm phát khi vượt $2–4M volume/tháng

## 7.5 Điều Kiện TGE
Token KHÔNG launch cho đến khi đạt đồng thời: ≥$500K volume/tháng, ≥1.000 trader, ≥10 market hoạt động. Chương trình Points trước TGE.

## 7.6 Áp Lực Mua vs Bán

| Áp lực mua | Tác động | Rủi ro bán | Giảm thiểu |
|---|---|---|---|
| Fee Buyback-Burn | CAO | Team dump sau cliff | 12M cliff + 36M linear |
| veToken Staking | CAO | LP mining emissions | Gaussian giảm dần |
| Market Creation Bond | TB | Investor dump TGE | TGE 10%, 6M cliff |
| LP Yield Boost | TB | Low utility pre-traction | Fee buyback từ Day 1 |
| Oracle Dispute Bond | THẤP | — | — |
