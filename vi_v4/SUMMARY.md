# Table of contents

* [Welcome](README.md)
  * [Tầm Nhìn & Sứ Mệnh](readme/tam-nhin-and-su-menh.md)
  * [Market Timing - Tại Sao Thời Điểm Này?](readme/market-timing-tai-sao-thoi-diem-nay.md)

## 📖 Cho Người Đọc Mới

* [Docs này dành cho ai?](doc-nay-danh-cho-ai.md)
* [Kiến Trúc Tổng Thể](kien-truc-tong-the.md)

## 🏢 Tổng Quan Dự Án

* [Vấn Đề Thị Trường](tong-quan-du-an/02-van-e-thi-truong.md)
* [Giải Pháp](tong-quan-du-an/03-giai-phap.md)
* [Tổng Quan Sản Phẩm](tong-quan-du-an/04-tong-quan-san-pham.md)
* [Cơ Chế Hoạt Động](tong-quan-du-an/05-co-che-hoat-ong.md)
* [Lớp Đầu Cơ (Speculation Layer)](tong-quan-du-an/06-lop-au-co-speculation-layer.md)
* [Tokenomics](tong-quan-du-an/07-tokenomics.md)
* [Hệ Sinh Thái NFT](tong-quan-du-an/08-he-sinh-thai-nft.md)
* [Mô Hình Doanh Thu](tong-quan-du-an/09-mo-hinh-doanh-thu.md)
* [Lợi Thế Cạnh Tranh](tong-quan-du-an/10-loi-the-canh-tranh.md)
* [Chiến Lược Tăng Trưởng (Go-to-Market)](tong-quan-du-an/11-chien-luoc-tang-truong-go-to-market.md)
* [Lộ Trình Phát Triển (Roadmap)](tong-quan-du-an/12-lo-trinh-phat-trien-roadmap.md)
* [Rủi Ro & Giảm Thiểu](tong-quan-du-an/13-rui-ro-giam-thieu.md)
* [Bối Cảnh Pháp Lý](tong-quan-du-an/boi-canh-phap-ly.md)
* [Team & Advisors](tong-quan-du-an/14-team-advisors.md)
* [References & Benchmarks](tong-quan-du-an/15-references-benchmarks.md)

## 🧑‍💻 Hướng Dẫn Người Dùng (Web2-friendly)

* [Kết nối ví](user-flow/ket-noi-vi.md)
* [Nhận USDC thử nghiệm](user-flow/nhan-usdc-thu-nghiem.md)
* [Giao dịch đầu tiên](user-flow/giao-dich-dau-tien.md)
* [Mua & Bán Share (Yes / No)](user-flow/mua-and-ban-share-yes-no.md)
* [Hiểu giao diện](user-flow/hieu-giao-dien.md)
* [Hủy lệnh](user-flow/huy-lenh.md)
* [Xem vị thế của bạn](user-flow/xem-vi-the-cua-ban.md)
* [Nhận thưởng](user-flow/nhan-thuong.md)
* [Nếu sai thì sao?](user-flow/neu-sai-thi-sao.md)
* [Tách & Gộp (Nâng cao)](user-flow/tach-and-gop-nang-cao.md)

## 📐 Khái Niệm Kỹ Thuật

* [Prediction Markets](khai-niem-cot-loi/16-prediction-markets.md)
* [Outcome Tokens (YES/NO)](khai-niem-cot-loi/17-outcome-tokens-yesno.md)
* [Split & Merge & Events](khai-niem-cot-loi/18-split-merge.md)
* [Resolution (Kết Quả Market)](khai-niem-cot-loi/19-resolution-giai-quyet-market.md)
* [Phí Giao Dịch & Redemption Fee Snapshot](khai-niem-cot-loi/20-phi-giao-dich.md)

## 📊 Cơ Chế Giao Dịch

* [Trading Overview](giao-dich/21-trading-overview.md)
* [Market Orders](giao-dich/22-market-orders.md)
* [Limit Orders](giao-dich/23-limit-orders.md)
* [Smart Routing](giao-dich/24-smart-routing.md)
* [Matching Engine (3 MatchType)](giao-dich/25-matching-engine.md)
* [Virtual NO Pricing](giao-dich/26-virtual-no-pricing.md)

## ⚙️ Smart Contracts

* [Contract Architecture](smart-contracts/27-contract-architecture.md)
* [Deployed Addresses](smart-contracts/deployed-addresses.md)
* [Diamond Proxy (EIP-2535)](smart-contracts/28-diamond-proxy-eip-2535.md)
* [MarketFacet](smart-contracts/market-facet.md)
* [EventFacet (Multi-Outcome)](smart-contracts/event-facet.md)
* [PrediXExchange (CLOB)](smart-contracts/29-exchange-clob.md)
* [PrediXRouter (Smart Router)](smart-contracts/30-router-smart-router.md)
* [PrediXHookV2 (Uniswap v4)](smart-contracts/31-hook-uniswap-v4.md)
* [Oracle](smart-contracts/32-oracle.md)
* [Access Control & Timelock](smart-contracts/33-access-control-safety.md)
* [Custom Errors Registry](smart-contracts/custom-errors.md)

## 🔌 Backend REST API (v2)

* [Overview](backend-api/00-overview.md)
* [Authentication (SIWE)](backend-api/01-authentication.md)
* [Response Envelope](backend-api/02-response-envelope.md)
* [Pagination, Filtering, Sorting](backend-api/03-pagination-filtering.md)
* [Rate Limits & Caching](backend-api/04-rate-limits-caching.md)
* [Markets Endpoints](backend-api/05-endpoints-markets.md)
* [Trading Endpoints](backend-api/06-endpoints-trading.md)
* [Users & Portfolio Endpoints](backend-api/07-endpoints-users.md)
* [Events Endpoints](backend-api/08-endpoints-events.md)
* [Error Codes](backend-api/09-error-codes.md)
* [OpenAPI Spec](backend-api/10-openapi-spec.md)

## 🗃️ Indexer (Ponder)

* [Overview](indexer/00-overview.md)
* [Schema (28 Tables)](indexer/01-schema.md)
* [REST API](indexer/02-rest-api.md)
* [Events Mapping (SC → Table)](indexer/03-events-mapping.md)
* [Reorg & Finality](indexer/04-reorg-finality.md)

## 👨‍💻 Developer Guides

* [Developer Quickstart](developers/34-developer-quickstart.md)
* [ABIs & Typed Clients](developers/39-abi-types.md)
* [Testnet Faucet & Setup](developers/40-testnet-faucet.md)
* [Trading Integration](developers/35-trading-integration.md)
* [Split, Merge, Resolve & Redeem](developers/36-split-merge-resolve-redeem.md)
* [Event Listening (On-chain + BE WS)](developers/41-event-listening.md)
* [Events Reference](developers/37-events.md)
* [Error Handling](developers/38-common-errors.md)
* [Integration Patterns](developers/42-integration-patterns.md)

## 🔐 Security

* [Overview](security/00-overview.md)
* [Audit Reports](security/01-audits.md)
* [Critical Invariants (7)](security/02-invariants.md)
* [Bug Bounty & Disclosure](security/03-bug-bounty.md)
* [Timelock & Upgrade Governance](security/04-timelock-governance.md)
* [Incident Playbook](security/05-incident-playbook.md)

## 📐 Design Documents

* [System Architecture](design-docs/00-system-architecture.md)
* [Smart Contract Specification](design-docs/01-smart-contract-spec.md)
* [Oracle Design Document](design-docs/02-oracle-design.md)
* [Market Resolution SOP](design-docs/03-market-resolution-sop.md)
* [Security Design Document](design-docs/04-security-design.md)
* [Partner & Integration API](design-docs/05-partner-api.md)
* [Multisig & Key Management Policy](design-docs/06-multisig-key-management.md)

## 📦 Tài Nguyên

* [FAQ](tai-nguyen/44-faq.md)
* [Glossary](tai-nguyen/45-glossary.md)
* [Changelog](tai-nguyen/changelog.md)
* [llms.txt (for AI agents)](tai-nguyen/llms.md)
* [Community & Links](tai-nguyen/46-community-links.md)
* [Brand Kit](tai-nguyen/47-brand-kit.md)
