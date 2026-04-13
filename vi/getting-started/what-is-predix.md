---
description: Tổng quan kiến trúc và điểm khác biệt chính của PrediX Protocol
---

# PrediX là gì?

PrediX Protocol là **nền tảng thị trường dự đoán phi tập trung** được xây dựng trên Uniswap v4 và triển khai trên Unichain L2. Nó cho phép bất kỳ ai tạo market về các sự kiện thực tế và giao dịch token kết quả trong môi trường on-chain hoàn toàn trustless.

## Điểm khác biệt chính

| Tính năng | PrediX | Khác |
| ------- | ------ | ------ |
| **Công cụ giao dịch** | Kết hợp CLOB + AMM + Smart Router | Chỉ CLOB (Polymarket) hoặc chỉ AMM (Augur) |
| **Token kết quả** | ERC-20 (tương thích với toàn bộ DeFi) | ERC-1155 (khả năng tổ hợp hạn chế) |
| **Hạ tầng** | Uniswap v4 Hooks + Flash Accounting | AMM tùy chỉnh hoặc cũ |
| **Nâng cấp** | Diamond Proxy (EIP-2535) | Triển khai lại hoặc không thể nâng cấp |
| **Hệ thống oracle** | Có thể cắm (Manual, Chainlink, mở rộng được) | Oracle đơn lẻ, cứng nhắc |
| **Bảo vệ LP** | Phí động: 0.5% → 5% gần hết hạn | Không bảo vệ |

## Tổng quan kiến trúc

```
                         User
                          │
              ┌───────────┼───────────┐
              ▼           ▼           ▼
         PrediXRouter  PrediXExchange  Diamond Proxy
         (Smart Route)  (CLOB)        (Market Management)
              │           │           │
              │     ┌─────┘     ┌─────┘
              ▼     ▼           ▼
         Uniswap v4        MarketFacet ─── OutcomeTokens (ERC-20)
         PoolManager       AccessControl
              │            OwnableFacet
              ▼            PausableFacet
         PrediXHookV1
         (Dynamic Fees,       Oracle Adapters
          Anti-Sandwich)      ├── ManualOracle
                              ├── ChainlinkAdapter
                              └── [Future: UMA, API3]
```

### Contract cốt lõi

| Contract | Vai trò |
| -------- | ---- |
| **Diamond** | Quản lý market: tạo, tách, gộp, giải quyết, nhận thưởng |
| **Exchange** | CLOB on-chain với matching engine bitmap 99-tick |
| **Router** | Định tuyến thông minh qua CLOB + AMM để khớp lệnh tối ưu |
| **Hook** | Uniswap v4 Hook: phí động, chống sandwich, đăng ký pool |
| **Oracle** | Hệ thống giải quyết có thể cắm (Manual, Chainlink, mở rộng được) |

---

**Tiếp theo**: [Cách hoạt động](how-it-works.md) · [Bắt đầu nhanh](quick-start.md) · [Khái niệm cốt lõi](../concepts/prediction-markets.md)
