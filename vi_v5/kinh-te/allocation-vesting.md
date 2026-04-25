# Phân bổ & vesting

Total supply: **1.000.000.000 PRX** (1B). Không mint thêm sau TGE.

## Phân bổ

| Nhóm | % | Tokens | Vesting | Ghi chú |
|---|---|---|---|---|
| **Community & Ecosystem** | 35% | 350M | Emission 48 tháng linear, unlock theo milestone gauge vote | Incentive user, LP, creator |
| **Treasury / DAO** | 20% | 200M | 4 năm linear, governance-controlled | Fund dev, audit, grants |
| **Team & Advisors** | 18% | 180M | 12 tháng cliff + 36 tháng linear | Core team + advisor |
| **Pre-Seed Investors** | 8% | 80M | 6 tháng cliff + 18 tháng linear, 10% TGE unlock | SAFT holders |
| **Seed Strategic** | 7% | 70M | 3 tháng cliff + 15 tháng linear, 10% TGE unlock | Strategic partners |
| **DEX/CEX Liquidity** | 7% | 70M | 50% TGE + 50% 12 tháng linear | Bootstrap thanh khoản exchange |
| **Protocol LP Seeding** | 5% | 50M | Unlock theo milestone volume | Seed AMM pool cho PrediX markets |

Tổng: **100%**.

## Circulating supply tại TGE

Dự kiến ~**12–15%** circulating (140–150M PRX):
- Pre-Seed 10%: 8M
- Seed 10%: 7M
- DEX/CEX 50%: 35M
- Community seeding first distribution: ~50M
- Treasury initial unlock: ~40M

Dilution thấp so với tiêu chuẩn ngành (thường 20–25% TGE). Mục tiêu: protect price từ dump ngay sau TGE.

## Lịch unlock (dự kiến)

```
Month  0 ──── TGE ─── ~12% circulating
Month  3 ──── +Seed linear start
Month  6 ──── +Pre-Seed linear start
Month 12 ──── +Team cliff end → team bắt đầu vest
Month 18 ──── Seed vest done
Month 24 ──── Pre-Seed vest done
Month 48 ──── Community emission complete
Month 48 ──── Team vest done
Month 48 ──── Treasury unlock done
```

Schedule cụ thể ngày tháng sẽ công bố tại TGE. Có thể điều chỉnh (thường hoãn không đẩy sớm) dựa trên market condition.

## Dilution per year

| Year | % circulating (cumulative) | Comments |
|---|---|---|
| 0 (TGE) | ~12% | Low circ, light float |
| 1 | ~30% | Team start vest, community scale |
| 2 | ~55% | Most investor vests done |
| 3 | ~80% | Team done, community emission winding |
| 4 | ~95% | Treasury near-complete |
| 5+ | 100% | Fully circulating |

## Team vesting hard

- **12 tháng cliff**: không nhận gì 12 tháng đầu sau TGE.
- **36 tháng linear**: vest hàng block từ cuối cliff.
- **Không có emergency unlock** cho team. Contract vesting is on-chain, verifiable.

Áp dụng cho toàn team + advisors. Founders không có allocation ngoài team pool.

## Lock / staking TGE

Tại TGE, sẽ có bonus APY cho ai lock sớm:
- Lock 6 tháng: +10% boost USDC yield.
- Lock 12 tháng: +25% boost + vePRX governance weight 2x.
- Lock 24 tháng: +50% boost + vePRX weight 4x.

Chi tiết: [vePRX & gauge](veprx-gauge.md).

## Anti-sybil & distribution rules

- Không có private sale sau Seed. Tiếp theo là TGE public.
- Airdrop community: testnet points → PRX theo formula công bố trước. Cap per wallet để chống sybil.
- OTC Seed/Pre-Seed chỉ ký với address KYC, không thể transfer trong cliff period (vesting contract enforce).

## Tham khảo

Cấu trúc này dựa trên:
- **Uniswap** (community + team + investor phân bổ 60/21/18).
- **Curve** (vePRX giống veCRV).
- **GMX** (real yield USDC).
- **Pendle** (vest thông minh).

PrediX khác:
- Community % thấp hơn Uniswap (35% vs 60%) vì Uniswap airdrop một lần lớn — PrediX trải đều 48 tháng emission để gắn user dài hạn.
- Team cliff dài hơn (12m vs 6m phổ biến) — giảm rủi ro team dump sớm.
- Investor % nhỏ (15% tổng vs 20–30% tiêu chuẩn) — giữ phần lớn supply cho community + treasury.
