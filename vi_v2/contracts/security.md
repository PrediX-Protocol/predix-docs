# Bảo mật

Kiến trúc bảo mật toàn diện bao gồm access control, safety cap, cơ chế khẩn cấp và kết quả audit.

## Access Control — 4 Role

| Role | ID | Quyền |
|------|----|-------------|
| `DEFAULT_ADMIN` | 0 | Cấp/thu hồi role, `diamondCut`, quyền sở hữu |
| `ADMIN` | 1 | Tạo market/category, approve oracle, set cap, resolve category |
| `OPERATOR` | 2 | Emergency resolve (sau 7d), bật refund mode |
| `PAUSER` | 3 | Pause/unpause từng facet hoặc toàn giao thức |

### Ma trận role

| Hàm | DEFAULT_ADMIN | ADMIN | OPERATOR | PAUSER | PUBLIC |
|----------|:---:|:---:|:---:|:---:|:---:|
| diamondCut | ✅ | | | | |
| createMarket | | ✅ | | | |
| setApprovedOracle | | ✅ | | | |
| setSafetyCaps | | ✅ | | | |
| resolveCategory | | ✅ | | | |
| emergencyResolve | | | ✅ | | |
| enableRefundMode | | | ✅ | | |
| pause / unpause | | | | ✅ | |
| splitPosition | | | | | ✅ |
| resolveMarket | | | | | ✅ |
| redeemMarketTokens | | | | | ✅ |
| placeOrder | | | | | ✅ |

### Bảo vệ quyền sở hữu

- **Chuyển quyền 2 bước:** `transferOwnership` → `acceptOwnership` tránh chuyển nhầm
- **Last-admin protection:** Không thể xoá `DEFAULT_ADMIN` cuối cùng

## Safety Caps

Ba cap cấu hình được để giới hạn rủi ro hệ thống:

| Cap | Hàm | Mục đích |
|-----|----------|---------|
| TVL Cap | `setTvlCap(uint256)` | Tổng USDC tối đa khoá trong giao thức |
| Per-Trade Cap | `setPerTradeCap(uint256)` | Kích thước lệnh tối đa |
| Per-Market Cap | `setPerMarketCap(uint256)` | Collateral tối đa cho mỗi market |

## Cơ chế khẩn cấp

| Cơ chế | Kích hoạt | Delay | Role |
|-----------|---------|-------|------|
| **Pause** | Sự cố bảo mật | Tức thì | PAUSER |
| **Emergency Resolve** | Oracle lỗi | 7 ngày sau endTime | OPERATOR |
| **Refund Mode** | Market không phân xử được | Tức thì | OPERATOR |

> ⚠️ Refund mode không đảo ngược được. Khi đã bật, market không resolve bình thường được nữa.

## Chống MEV

- **Flashblocks 200ms:** Block siêu nhanh của Unichain giảm cửa sổ front-run
- **TEE ordering:** Trusted Execution Environment ngăn reorder độc hại
- **Phí động:** Phí 5% gần expiry làm sandwich attack không có lời

## Reentrancy Guard

Ba lớp bảo vệ reentrancy trên mọi external function:

1. **OpenZeppelin ReentrancyGuard** trên các Diamond facet
2. **Uniswap v4 lock pattern** trên Hook callback
3. **Custom guard** trên khớp lệnh Exchange

## Kết quả audit

| Chỉ số | Kết quả |
|--------|--------|
| Unit + integration test | 264 pass |
| Audit AI độc lập | 3 (cross-verify) |
| Slither static analysis | 0 lỗi thực |
| Fuzz testing | 14 chiến dịch × 10,000 run |
| On-chain E2E | 31 test trên Unichain Sepolia |
| Bug đã sửa | 22 |
| Điểm OWASP | 10/10 |

### Một số bug đã sửa

- Reentrancy trong khớp lệnh nhiều bước → thêm custom guard
- Overflow giá tại tick biên → thêm bounds check
- Thiếu access control trên setter admin → thêm role check
- Flash loan attack lên split → thêm same-block protection

Báo cáo lỗ hổng mới qua [Bug Bounty](../security/bug-bounty.md).

## Tiếp theo

- [Bug Bounty](../security/bug-bounty.md) — báo cáo lỗ hổng
- [Diamond](diamond.md) — quy trình nâng cấp
- [Oracle](oracle.md) — kiến trúc oracle
