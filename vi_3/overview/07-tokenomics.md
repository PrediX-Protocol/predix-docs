# 7. Tokenomics

Tổng cung: 1.000.000.000 PRX | Giá khởi điểm: $0,015 | FDV tại TGE: $15M

### 7.1 Phân Bổ Token

<table><thead><tr><th width="248">Nhóm</th><th width="96">%</th><th width="111">Tokens</th><th>Vesting</th></tr></thead><tbody><tr><td>Community &#x26; Ecosystem</td><td>35%</td><td>350M</td><td>Emission giảm dần 48 tháng</td></tr><tr><td>Treasury / DAO</td><td>20%</td><td>200M</td><td>DAO kiểm soát, 4 năm</td></tr><tr><td>Team &#x26; Advisors</td><td>18%</td><td>180M</td><td>12M cliff + 36M linear (48M tổng)</td></tr><tr><td>Pre-Seed Investors</td><td>8%</td><td>80M</td><td>6M cliff + 18M linear, TGE 10%</td></tr><tr><td>Seed Strategic</td><td>7%</td><td>70M</td><td>3M cliff + 15M linear, TGE 10%</td></tr><tr><td>DEX/CEX Liquidity</td><td>7%</td><td>70M</td><td>50% TGE, 50% trong 12 tháng</td></tr><tr><td>Protocol Liquidity Seeding</td><td>5%</td><td>50M</td><td>Theo milestone mainnet</td></tr></tbody></table>

50% tổng cung cho community/ecosystem. Vesting team 48 tháng dài hơn tiêu chuẩn ngành 24 tháng.

### 7.2 Bảy Tiện Ích Token (Token Utility)

### 7.2.1 Staking & Chia Sẻ Doanh Thu

* **Cơ chế:** Stake PRX → nhận 50% tổng phí giao dịch protocol bằng USDC
* **Real yield:** Doanh thu từ phí thật, không phải token lạm phát. APY scale tuyến tính theo volume
* **Ví dụ:** $500K volume/tháng × 1% phí trung bình = $5K USDC/tháng cho pool staker
* **Activation:** P0 — từ TGE
* **Ref:** dYdX V4 (100% phí → staker), GMX (30% phí → staker GLP). PrediX chọn mô hình 50/30/20 cân bằng giữa staker, buyback-burn và treasury

### 7.2.2 Oracle & Giải Quyết Tranh Chấp

* **Cơ chế:** Stake PRX để vote kết quả tranh chấp qua commit-reveal voting (hash trước, reveal sau để chống front-running)
* **Incentive:** Vote đúng → nhận thưởng từ dispute bond. Vote sai → bị slash theo tỷ lệ
* **Escalation:** Tranh chấp vượt 10% vốn hoá PRX → chuyển lên multi-sig
* **Price markets:** Tự động resolve qua Chainlink feeds (VD: “BTC > $200K”)
* **Activation:** P1 — từ mainnet
* **Ref:** UMA DVM (Optimistic Oracle + bond mechanism). PrediX mở rộng bằng commit-reveal thay vì optimistic window

### 7.2.3 Gauge Voting (vePRX)

* **Cơ chế:** Khoá PRX → nhận vePRX (khoá 1–12 tháng, khoá lâu = nhiều voting power hơn)
* **Tác dụng:** vePREDIX holders vote hàng tuần market nào nhận trợ cấp LP từ treasury
* **Flywheel:** Market được vote nhiều → thanh khoản sâu → volume cao → phí nhiều → yield staker tăng → demand PRX tăng
* **Differentiator:** “Curve Wars” cho prediction markets — không đối thủ nào có cơ chế này
* **Activation:** P0 — từ TGE
* **Ref:** Curve veCRV (khoá CRV → vote gauge → $4B TVL locked). Balancer veBAL. PrediX áp dụng mô hình đã proven cho vertical mới

### 7.2.4 Giảm Phí Theo Tầng

* **Cơ chế:** Stake PRX đạt ngưỡng → giảm phí giao dịch 20–50%
* **Điều kiện giữ tầng:** Kết hợp với volume giao dịch tối thiểu hàng tháng
* **Tầng (dự kiến):** Bronze (giảm 10%), Silver (giảm 20%), Gold (giảm 35%), Diamond (giảm 50%)
* **Activation:** P0 — từ TGE
* **Ref:** Hyperliquid (fee tiers theo volume + staking). BNB fee discount trên Binance. PrediX kết hợp staking + volume thay vì chỉ volume

### 7.2.5 Buyback-Burn

* **Cơ chế:** 30% doanh thu protocol → buyback PREDIX tự động trên thị trường mở → burn vĩnh viễn. On-chain verifiable
* **Liên kết trực tiếp:** Sử dụng nền tảng tăng → burn rate tăng → cung giảm → price floor tự nhiên
* **Ước tính:** 1–3% tổng cung bị burn mỗi năm tại quy mô dự phóng
* **Activation:** P0 — từ TGE (từ ngày đầu có volume)
* **Ref:** Hyperliquid (97% phí → buyback = $49B FDV, $716M burn năm 2025). BNB quarterly burn. PrediX dùng 30% (thay vì 97% như Hyperliquid) để cân bằng với staker yield

### 7.2.6 Bond Tạo Market

* **Cơ chế:** Stake 10.000 PRX để tạo market không cần phép (permissionless)
* **Incentive:** Resolve hợp lệ → hoàn bond + thưởng (5–10% phí market). Spam/thao túng → slash toàn bộ bond
* **Tác dụng:** Bộ lọc chất lượng + tạo demand cấu trúc cho PREDIX khi số lượng market scale
* **Activation:** P1 — M+3 sau mainnet
* **Ref:** Azuro (pool creation bond). Hyperliquid HIP-3 (listing bond). PrediX kết hợp bond + revenue share cho market creator

### 7.2.7 Collateral Yield Sharing

* **Cơ chế:** vePRX holders nhận phần chia từ yield trên idle USDC collateral (70% USDC protocol → Aave v3 \~4% APY)
* **Tác dụng:** Tạo thêm nguồn real yield ngoài phí giao dịch, scale theo TVL thay vì volume
* **Ví dụ:** Tại TVL $50M → \~$1,4M yield/năm → chia cho vePRX holders
* **Activation:** P2 — M+12 sau TGE
* **Ref:** GMX (GLP yield sharing -70% phí cho staker). Polymarket giữ $416M USDC nhàn rỗi ở 0% yield — PrediX biến thành nguồn doanh thu

### 7.2.8 Activation Priority Roadmap

<table><thead><tr><th>Utility</th><th width="67">Priority</th><th width="135">Timeline</th><th>Impact</th><th>Benchmark</th></tr></thead><tbody><tr><td>Fee Buyback-Burn</td><td>P0</td><td>Từ TGE</td><td>CAO - price floor</td><td>Hyperliquid: 97% fee buyback</td></tr><tr><td>veToken Staking (Gauge)</td><td>P0</td><td>Từ TGE</td><td>CAO - giảm cung lưu hành</td><td>Curve: veCRV model</td></tr><tr><td>LP Yield Boost</td><td>P0</td><td>Từ TGE</td><td>CAO - giữ chân LP</td><td>Curve/Balancer gauge</td></tr><tr><td>Giảm phí theo tầng</td><td>P0</td><td>Từ TGE</td><td>CAO - incentive trade</td><td>Hyperliquid fee tiers</td></tr><tr><td>Market Creation Bond</td><td>P1</td><td>M+3 sau mainnet</td><td>TB - scale demand</td><td>Azuro: pool creation</td></tr><tr><td>Oracle Dispute Bond</td><td>P1</td><td>Từ mainnet</td><td>TB - an toàn hệ thống</td><td>UMA: bond mechanism</td></tr><tr><td>NFT Pass Access</td><td>P2</td><td>M+6 sau TGE</td><td>TB - gamification</td><td>Blur: NFT + token model</td></tr><tr><td>Collateral Yield Sharing</td><td>P2</td><td>M+12 sau TGE</td><td>CAO dài hạn</td><td>GMX: GLP yield sharing</td></tr></tbody></table>

### 7.3 Phân Bổ Doanh Thu

<table><thead><tr><th>Phân bổ</th><th width="162">%</th><th>Cơ chế</th></tr></thead><tbody><tr><td>PREDIX Staker</td><td>50%</td><td>USDC real yield</td></tr><tr><td>Buyback-Burn</td><td>30%</td><td>Mua → burn vĩnh viễn</td></tr><tr><td>Treasury</td><td>20%</td><td>Vận hành + trợ cấp LP gauge</td></tr></tbody></table>

### 7.4 Cơ Chế Giảm Phát

* **Giảm cung:** Buyback-burn (1–3% cung/năm), oracle slash, bond slash
* **Tăng cung:** Oracle rewards \~0,5%/năm, staking emission gần 0
* **Kết quả ròng:** Giảm phát khi vượt $2–4M volume/tháng

### 7.5 Điều Kiện TGE

Token KHÔNG launch cho đến khi đạt đồng thời: ≥$500K volume/tháng, ≥1.000 trader, ≥10 market hoạt động. Chương trình Points trước TGE.

### 7.6 Áp Lực Mua vs Bán

<table><thead><tr><th>Áp lực mua</th><th width="114">Tác động</th><th>Rủi ro bán</th><th>Giảm thiểu</th></tr></thead><tbody><tr><td>Fee Buyback-Burn</td><td>CAO</td><td>Team dump sau cliff</td><td>12M cliff + 36M linear</td></tr><tr><td>veToken Staking</td><td>CAO</td><td>LP mining emissions</td><td>Gaussian giảm dần</td></tr><tr><td>Market Creation Bond</td><td>TB</td><td>Investor dump TGE</td><td>TGE 10%, 6M cliff</td></tr><tr><td>LP Yield Boost</td><td>TB</td><td>Low utility pre-traction</td><td>Fee buyback từ Day 1</td></tr><tr><td>Oracle Dispute Bond</td><td>THẤP</td><td>—</td><td>—</td></tr></tbody></table>
