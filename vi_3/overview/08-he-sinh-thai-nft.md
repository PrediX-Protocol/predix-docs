# 8. Hệ Sinh Thái NFT

Chi phí mint trên Unichain L2: \~$0,01–$0,05/NFT. Gamification tăng engagement 47%, loyalty 22% (nghiên cứu Mintology). Không đối thủ prediction market nào có lớp gamification hay NFT. PrediX xây dựng hệ sinh thái NFT với hai lớp chức năng: Identity/Gamification (tăng retention, tạo danh tính on-chain) và Tài Sản Tài Chính (tokenize vị thế, tạo thị trường thứ cấp).

### 8.1 Lớp Identity & Gamification

### Trader Passport — Hộ Chiếu Trader (Soulbound, ERC-5192)

* **Mô tả:** Mỗi user mint 1 Soulbound NFT không chuyển nhượng khi đăng ký -“hộ chiếu trader” ghi lại on-chain: tổng volume, số market, tỷ lệ thắng, streak dài nhất, ngày tham gia
* **Tiến hoá visual:** Bronze → Silver → Gold → Diamond — tự động nâng cấp theo hoạt động
* **Utility:** Passport level quyết định tầng phí giao dịch, quyền truy cập sớm market mới, và eligibility cho airdrop. Diamond Passport = ưu tiên whitelist cho mọi event protocol
* **Tại sao Soulbound:** Không thể fake, không thể mua bán — danh tính on-chain thật sự
* **Ref:** Galxe Passport (identity layer cho DeFi), Lens Protocol (social graph NFT). PrediX chuyên biệt cho prediction market với metrics trading cụ thể

### Achievement Badges — Huy Hiệu Thành Tích (ERC-1155)

Mô tả: Semi-fungible badges mint tự động khi đạt milestone Danh sách badges:

* First Prediction (Common) — trade lần đầu
* Sniper (Uncommon) -5 dự đoán đúng liên tiếp
* Whale (Rare) ->$10K trong 1 market
* Oracle (Epic) -10+ lần vote oracle đúng
* Season Champion (Legendary) — top leaderboard mùa
* **Utility:** Combo badges mở khoá perks: 3 Rare → Pro Trader status (giảm phí 15% + badge hiển thị trên profile). 5 Epic → Elite Trader (truy cập analytics nâng cao)
* **Ref:** Blur Season badges (drive volume qua gamification), Rabbithole (quest rewards). PrediX gắn badge trực tiếp với fee discount thay vì chỉ cosmetic

### Season Competition & Trophy

* **Mô tả:** Mỗi season 3 tháng với leaderboard theo volume, win rate và P\&L
* **Utility:** Top performers nhận Trophy NFTs mở khoá: giảm phí 20–30%, bonus PREDIX airdrop, quyền truy cập sớm market premium, và badge độc quyền không bao giờ mint lại
* **Ref:** Mô hình Season Pass đã proven trong gaming (Fortnite: $5,8B doanh thu từ battle pass). Hyperliquid Points Season. PrediX áp dụng cho prediction market — phù hợp văn hoá gaming APAC

### Referral NFT — Giới Thiệu (Soulbound)

* **Mô tả:** Tiến hoá theo số giới thiệu thành công
* **Tầng:** Connector (1–5 refs) → Ambassador (6–20) → Community Leader (21–50) → Network Builder (50+)
* **Utility:** Network Builder nhận % phí giao dịch vĩnh viễn từ tất cả referred users. Ambassador+ được invite-only access vào community riêng và early alpha trên market mới
* **Ref:** Blur referral (% fee sharing vĩnh viễn). Hyperliquid referral program (5% fee). PrediX kết hợp NFT tiến hoá + fee sharing + CoinCraze 100K+ network sẵn có

### 8.2 Lớp Tài Sản Tài Chính

#### LP Position NFT (ERC-721)

* **Mô tả:** Biểu diễn vị thế thanh khoản trên prediction market pool
* **Metadata on-chain:** Market ID, USDC đã gửi, pool share %, phí tích luỹ, range LP
* **Utility:** Chuyển nhượng được → tạo thị trường thứ cấp cho việc thoát LP mà không cần remove liquidity. Tương lai (Phase 3): dùng làm collateral trên lending protocols để vay USDC
* **Ref:** Uniswap V3 LP NFTs (tokenize vị thế concentrated liquidity). PrediX mở rộng cho prediction market pools với metadata market-specific

#### Limit Order Receipt NFT (ERC-721)

* **Mô tả:** Wrapper cho ERC-6909 limit order claims trên CLOB
* **Metadata:** Loại lệnh (BUY/SELL), token (YES/NO), giá, số lượng, trạng thái (active/filled/cancelled)
* **Utility:** Chuyển nhượng được → tạo thị trường OTC cho vị thế limit order. Trader có thể bán quyền claim lệnh chưa fill thay vì cancel
* **Ref:** Không có tiền lệ trực tiếp — PrediX là protocol đầu tiên tokenize limit orders thành NFT tradeable

#### Market Founder NFT (Phase 3, ERC-721)

* **Mô tả:** Mint khi tạo market (yêu cầu PREDIX bond)
* **Utility:** Founder nhận 5–10% phí giao dịch vĩnh viễn từ market mình tạo. Dynamic art tiến hoá từ hạt giống → cây con → cây lớn theo volume — thể hiện visual sự phát triển của market
* **Tác dụng:** Khuyến khích nền kinh tế creator cho prediction market. Market creators trở thành stakeholders có incentive dài hạn
* **Ref:** Mirror Writing NFTs (creator revenue share). Azuro pool creator incentives. PrediX thêm dynamic art + perpetual fee share

### 8.3 NFT Utility Summary

<table><thead><tr><th>NFT</th><th>Chuẩn</th><th width="128">Chuyển nhượng</th><th>Utility chính</th><th>Ref</th></tr></thead><tbody><tr><td>Trader Passport</td><td>ERC-5192</td><td>Không (Soulbound)</td><td>Fee tier, whitelist, eligibility</td><td>Galxe, Lens</td></tr><tr><td>Achievement Badge</td><td>ERC-1155</td><td>Có</td><td>Combo perks, Pro/Elite status</td><td>Blur, Rabbithole</td></tr><tr><td>Season Trophy</td><td>ERC-721</td><td>Có</td><td>Giảm phí, airdrop bonus</td><td>Fortnite Pass, Hyperliquid</td></tr><tr><td>Referral NFT</td><td>ERC-5192</td><td>Không (Soulbound)</td><td>% phí vĩnh viễn, early access</td><td>Blur, Hyperliquid ref</td></tr><tr><td>LP Position</td><td>ERC-721</td><td>Có</td><td>Thị trường thứ cấp LP, collateral</td><td>Uniswap V3 LP NFT</td></tr><tr><td>Limit Order Receipt</td><td>ERC-721</td><td>Có</td><td>OTC market cho limit orders</td><td>Không tiền lệ</td></tr><tr><td>Market Founder</td><td>ERC-721</td><td>Có</td><td>5–10% phí vĩnh viễn, dynamic art</td><td>Mirror, Azuro</td></tr></tbody></table>
