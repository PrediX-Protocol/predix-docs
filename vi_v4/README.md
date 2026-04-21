---
description: PrediX V2 — Prediction market protocol trên Unichain. Hybrid AMM + CLOB, non-custodial, composable.
---

# Welcome

{% hint style="info" %}
**Docs này phục vụ 5 nhóm người đọc**: người dùng web2, developer/integrator, nhà đầu tư (VC), grant reviewer, và người quan tâm marketing/community. Mỗi section có badge chỉ rõ audience. Xem [Docs này dành cho ai?](doc-nay-danh-cho-ai.md) để biết bắt đầu từ đâu.
{% endhint %}

## PrediX là gì?

PrediX là **prediction-market protocol hoàn toàn on-chain**, xây dựng trên [Unichain](https://www.unichain.org/) (L2 do Uniswap vận hành). Người dùng mua/bán outcome tokens (YES/NO) bằng USDC để thể hiện dự đoán về các sự kiện thực (bầu cử, giá crypto, thể thao, pop-culture).

Khác với đối thủ, PrediX là protocol **hybrid liquidity**: kết hợp AMM (Uniswap v4 Hook) với CLOB (Central Limit Order Book on-chain), điều phối bằng Smart Router. Outcome tokens là **ERC-20** — composable với toàn bộ DeFi (LP, collateral, vault).

## 3 câu tóm gọn

**What** — Prediction market on-chain, non-custodial, outcome tokens ERC-20, hybrid AMM+CLOB.

**Why** — Thị trường $64B (2025) → dự báo $200–325B (2026). Polymarket bị cấm 33+ quốc gia, Kalshi chỉ phục vụ Mỹ. APAC có 38M+ crypto users nhưng chưa có prediction market bản địa.

**How** — Tạo market → Split USDC thành YES+NO → Trade qua Router (CLOB+AMM) → Oracle resolve → Redeem USDC. Diamond proxy (EIP-2535) cho nâng cấp modular; Hook v4 cho AMM; Exchange cho limit orders.

## Bắt đầu nhanh

{% tabs %}
{% tab title="🧑 Người dùng mới" %}
Đọc [Hướng Dẫn Người Dùng](user-flow/ket-noi-vi.md) — 10 bước ngắn từ kết nối ví đến rút thưởng. Không cần biết blockchain.
{% endtab %}

{% tab title="👨‍💻 Developer" %}
Đọc [Developer Quickstart](developers/34-developer-quickstart.md) — mua YES trong 5 phút. Sau đó [ABIs & Typed Clients](developers/39-abi-types.md) và [Backend API](backend-api/00-overview.md).
{% endtab %}

{% tab title="💰 VC / Grant" %}
Đọc [Tổng Quan Dự Án](tong-quan-du-an/04-tong-quan-san-pham.md) → [Tokenomics](tong-quan-du-an/07-tokenomics.md) → [Mô Hình Doanh Thu](tong-quan-du-an/09-mo-hinh-doanh-thu.md) → [Roadmap](tong-quan-du-an/12-lo-trinh-phat-trien-roadmap.md).
{% endtab %}

{% tab title="🔐 Auditor" %}
Đọc [Contract Architecture](smart-contracts/27-contract-architecture.md) → [Invariants](security/02-invariants.md) → [Audit Reports](security/01-audits.md).
{% endtab %}
{% endtabs %}

## Kiến trúc một dòng

```
FE (Next.js) ⟶ BE REST API v2 (NestJS) ⟶ Indexer (Ponder) ⟶ Smart Contracts (Unichain)
                                                ↑
                                        Events từ Diamond, Hook, Exchange, Router, Oracle
```

Chi tiết: [Kiến Trúc Tổng Thể](kien-truc-tong-the.md).

## Quick Links

| Resource | URL |
| --- | --- |
| Website | predixpro.io |
| App (testnet) | app.predixpro.io |
| Docs | docs.predixpro.io |
| GitHub | github.com/predix-protocol |
| Testnet Faucet | [Faucet Guide](developers/40-testnet-faucet.md) |
| API OpenAPI spec | [openapi/v2.json](backend-api/10-openapi-spec.md) |
| Bug bounty | [Disclosure](security/03-bug-bounty.md) |
| Contract addresses | [Deployed Addresses](smart-contracts/deployed-addresses.md) |

{% hint style="warning" %}
**Trạng thái hiện tại**: **Testnet** (Unichain Sepolia, Chain ID 1301). Mainnet launch pending external audit. Mọi address trong docs này là testnet. Không bao giờ gửi tiền thật tới testnet address.
{% endhint %}
