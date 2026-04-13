---
description: Kiến trúc hợp đồng thông minh và địa chỉ triển khai
---

# Tổng quan Hợp đồng

## Kiến trúc

PrediX Protocol bao gồm 5 hợp đồng cốt lõi:

| Hợp đồng | Địa chỉ (Unichain Sepolia) | Vai trò |
| -------- | -------------------------- | ---- |
| **Diamond** | `0xF38a265E6e4F57D000a1CC08004da5B4A380B08A` | Quản lý thị trường (EIP-2535 proxy) |
| **Hook** | `0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0` | Uniswap v4 Hook: phí động + chống tấn công sandwich |
| **Exchange** | `0xa202abCb2A358c0862B2dA76b553398339F2C638` | Sổ lệnh CLOB on-chain |
| **Router** | `0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7` | Định tuyến thông minh: tổng hợp CLOB + AMM |
| **Oracle** | `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3` | ManualOracleAdapter |

### Phụ thuộc bên ngoài

| Hợp đồng | Địa chỉ |
| -------- | ------- |
| USDC | `0x12fd156C8b5F2901BA2781d97db84AaC56b2b911` |
| Uniswap v4 PoolManager | `0x00B036B58a818B1BC34d502D3fE730Db729e62AC` |

## Tại sao sử dụng Diamond Proxy (EIP-2535)

- **Vượt qua giới hạn 24KB**: Logic giao thức phức tạp vượt quá giới hạn kích thước hợp đồng EVM
- **Nâng cấp chi tiết**: Thay thế các hàm riêng lẻ mà không cần triển khai lại toàn bộ
- **Lưu trữ chia sẻ**: Tất cả Facet chia sẻ cùng một bố cục lưu trữ
- **Đã được kiểm chứng trong sản xuất**: Được sử dụng bởi Aavegotchi, Beanstalk và các dự án khác

## Thông tin chuỗi

| Tham số | Giá trị |
| --------- | ----- |
| Mạng | Unichain Sepolia (Testnet) |
| Chain ID | `1301` |
| RPC | `https://sepolia.unichain.org` |
| Trình khám phá | `https://sepolia.uniscan.xyz` |

---

**Tiếp theo**: [Hợp đồng Diamond](diamond.md) · [Exchange](exchange.md) · [Router](router.md) · [Hook](hook.md) · [Oracle](oracle.md)
