---
description: Bản đồ đọc docs theo persona — tìm đúng section bạn cần trong 30 giây.
---

# Docs này dành cho ai?

Docs PrediX phục vụ **5 nhóm người đọc**. Chọn nhóm gần bạn nhất để biết bắt đầu từ đâu.

---

## 🧑 Người dùng web2 (trader phổ thông)

**Bạn cần biết**: cách kết nối ví, mua-bán token YES/NO, đọc giao diện, rút thưởng.

**Không cần biết**: blockchain, smart contract, API.

**Đường đọc đề xuất**:

1. [Kết nối ví](user-flow/ket-noi-vi.md) → [Nhận USDC thử nghiệm](user-flow/nhan-usdc-thu-nghiem.md) → [Giao dịch đầu tiên](user-flow/giao-dich-dau-tien.md)
2. [Mua & Bán Share](user-flow/mua-and-ban-share-yes-no.md) → [Hiểu giao diện](user-flow/hieu-giao-dien.md)
3. [Xem vị thế](user-flow/xem-vi-the-cua-ban.md) → [Nhận thưởng](user-flow/nhan-thuong.md) → [Nếu sai thì sao?](user-flow/neu-sai-thi-sao.md)

**Section nên bỏ qua**: Smart Contracts, Backend API, Indexer, Security (trừ FAQ).

---

## 👨‍💻 Developer / Integrator

Chia theo vai trò cụ thể:

### App Builder (FE / Widget / dApp)

Bạn tích hợp PrediX vào app/widget, gọi BE API để list market + user data, gọi Router on-chain để trade.

**Đọc theo thứ tự**:

1. [Kiến Trúc Tổng Thể](kien-truc-tong-the.md)
2. [Developer Quickstart](developers/34-developer-quickstart.md)
3. [Backend API Overview](backend-api/00-overview.md) → [Authentication](backend-api/01-authentication.md) → [Markets endpoints](backend-api/05-endpoints-markets.md)
4. [Trading Integration](developers/35-trading-integration.md)
5. [Integration Patterns → For App Builders](developers/42-integration-patterns.md#for-app-builders)

### Market Maker (LP + CLOB maker)

Bạn cung cấp thanh khoản AMM và/hoặc post limit orders trên CLOB.

**Đọc theo thứ tự**:

1. [Matching Engine](giao-dich/25-matching-engine.md) + [Virtual NO Pricing](giao-dich/26-virtual-no-pricing.md)
2. [PrediXExchange (CLOB)](smart-contracts/29-exchange-clob.md) + [PrediXHookV2](smart-contracts/31-hook-uniswap-v4.md)
3. [Phí Giao Dịch](khai-niem-cot-loi/20-phi-giao-dich.md)
4. [Integration Patterns → For Market Makers](developers/42-integration-patterns.md#for-market-makers)
5. [Event Listening](developers/41-event-listening.md) cho fill monitoring

### Bot / Algo Trader

Bạn chạy bot đọc orderbook, giá, positions qua API và trade qua Router.

**Đọc theo thứ tự**:

1. [Developer Quickstart](developers/34-developer-quickstart.md)
2. [Rate Limits & Caching](backend-api/04-rate-limits-caching.md)
3. [Trading Endpoints](backend-api/06-endpoints-trading.md) + [Error Codes](backend-api/09-error-codes.md)
4. [Error Handling](developers/38-common-errors.md)
5. [Integration Patterns → For Bots](developers/42-integration-patterns.md#for-bots--algo-traders)

### Protocol Developer (build on top)

Bạn triển khai oracle adapter, hook variant, aggregator, hoặc build composable protocol gọi PrediX.

**Đọc theo thứ tự**:

1. [Contract Architecture](smart-contracts/27-contract-architecture.md) → toàn bộ section `Smart Contracts`
2. [Custom Errors Registry](smart-contracts/custom-errors.md)
3. [Invariants](security/02-invariants.md)
4. [Timelock & Upgrade Governance](security/04-timelock-governance.md)
5. [Integration Patterns → For Protocol Developers](developers/42-integration-patterns.md#for-protocol-developers)

---

## 💰 Nhà đầu tư (VC) / Grant reviewer

**Bạn cần biết**: cơ hội thị trường, competitive moat, tokenomics, revenue model, roadmap, team, risks, traction metrics.

**Không cần biết**: chi tiết implementation, REST endpoints, event payload.

**Đường đọc đề xuất**:

1. [Vấn Đề Thị Trường](tong-quan-du-an/02-van-e-thi-truong.md) → [Giải Pháp](tong-quan-du-an/03-giai-phap.md) → [Tổng Quan Sản Phẩm](tong-quan-du-an/04-tong-quan-san-pham.md)
2. [Lợi Thế Cạnh Tranh](tong-quan-du-an/10-loi-the-canh-tranh.md) → [Chiến Lược Tăng Trưởng](tong-quan-du-an/11-chien-luoc-tang-truong-go-to-market.md)
3. [Tokenomics](tong-quan-du-an/07-tokenomics.md) → [Mô Hình Doanh Thu](tong-quan-du-an/09-mo-hinh-doanh-thu.md)
4. [Roadmap](tong-quan-du-an/12-lo-trinh-phat-trien-roadmap.md) → [Rủi Ro & Giảm Thiểu](tong-quan-du-an/13-rui-ro-giam-thieu.md)
5. [Bối Cảnh Pháp Lý](tong-quan-du-an/boi-canh-phap-ly.md) → [Team & Advisors](tong-quan-du-an/14-team-advisors.md)
6. Optional deep dive: [Kiến Trúc Tổng Thể](kien-truc-tong-the.md) + [Audit Reports](security/01-audits.md) để verify tech claims.

---

## 🔐 Security auditor / Researcher

**Bạn cần biết**: threat model, invariants, upgrade authority, known issues, audit firm + report, bounty.

**Đường đọc đề xuất**:

1. [Security Overview](security/00-overview.md)
2. [Contract Architecture](smart-contracts/27-contract-architecture.md) + [toàn bộ section Smart Contracts]
3. [Critical Invariants (7)](security/02-invariants.md)
4. [Audit Reports](security/01-audits.md)
5. [Timelock & Upgrade Governance](security/04-timelock-governance.md)
6. [Custom Errors Registry](smart-contracts/custom-errors.md)
7. [Incident Playbook](security/05-incident-playbook.md) + [Bug Bounty](security/03-bug-bounty.md)

Source code: [github.com/predix-protocol](https://github.com/predix-protocol).

---

## 📣 Marketing / Community / Journalist

**Bạn cần biết**: tagline, one-liner, khác biệt, screenshot/brand kit, traction.

**Đường đọc đề xuất**:

1. [Welcome](README.md) — tagline + 3 câu tóm gọn
2. [Tầm Nhìn & Sứ Mệnh](readme/tam-nhin-and-su-menh.md)
3. [Market Timing](readme/market-timing-tai-sao-thoi-diem-nay.md)
4. [Tổng Quan Sản Phẩm](tong-quan-du-an/04-tong-quan-san-pham.md) + [Lợi Thế Cạnh Tranh](tong-quan-du-an/10-loi-the-canh-tranh.md)
5. [Brand Kit](tai-nguyen/47-brand-kit.md) cho logo, colors, screenshots
6. [Community & Links](tai-nguyen/46-community-links.md)

---

## 🤖 AI agent / LLM

Nếu bạn là AI coding assistant / LLM agent được giao task liên quan đến PrediX — mở [`llms.txt`](tai-nguyen/llms.md). File này tổng hợp concept + addresses + endpoint list cô đọng cho LLM ingest, không cần đọc từng page.

---

## Bảng định hướng nhanh

| Bạn đang muốn… | Mở section |
|---|---|
| Trade thử trên testnet | [User Flow](user-flow/ket-noi-vi.md) |
| Lấy contract address | [Deployed Addresses](smart-contracts/deployed-addresses.md) |
| Biết có endpoint nào | [Backend API Overview](backend-api/00-overview.md) |
| Lấy ABI | [ABIs & Typed Clients](developers/39-abi-types.md) |
| Hiểu cơ chế YES/NO pricing | [Virtual NO Pricing](giao-dich/26-virtual-no-pricing.md) |
| Xem audit report | [Audit Reports](security/01-audits.md) |
| Hiểu upgrade governance | [Timelock & Upgrade](security/04-timelock-governance.md) |
| Đầu tư / grant | [Tổng Quan Dự Án](tong-quan-du-an/04-tong-quan-san-pham.md) |
| Report bug | [Bug Bounty](security/03-bug-bounty.md) |
| Dùng AI agent | [llms.txt](tai-nguyen/llms.md) |
