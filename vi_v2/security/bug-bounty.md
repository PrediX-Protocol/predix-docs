# Bug Bounty

PrediX vận hành chương trình công bố có trách nhiệm cho toàn bộ smart contract đã deploy.

## Scope

Mọi contract đã deploy trên Unichain Sepolia Testnet:

| Contract | Địa chỉ |
|----------|---------|
| Diamond | `0xF38a265E6e4F57D000a1CC08004da5B4A380B08A` |
| Hook | `0xAe7eA7eba1D3B0815dCA2b43f250428c20ed30c0` |
| Exchange | `0xa202abCb2A358c0862B2dA76b553398339F2C638` |
| Router | `0xEfc57eB2b5b5BE7E5b8377be23f8D31354811Eb7` |
| Oracle | `0x699A8C74663b1C852E195b2ffa00D5965E992Cf3` |

## Mức độ & phần thưởng

| Mức | Mô tả | Phần thưởng |
|----------|-------------|--------|
| **Critical** | Mất tiền, mint trái phép, thao túng oracle | Tối đa $50,000 |
| **High** | Griefing, DoS vĩnh viễn, bypass access control | Tối đa $10,000 |
| **Medium** | DoS tạm thời, tính phí sai, state không nhất quán | Tối đa $5,000 |
| **Low** | Kém hiệu quả gas, lỗi view nhỏ | Tối đa $1,000 |

## Cách báo cáo

1. **Email:** security@predix.io
2. **Bao gồm:** Mô tả, cách tái hiện, đánh giá tác động, đề xuất fix
3. **Không** công bố công khai trước khi fix được deploy
4. **Không** khai thác lỗ hổng trên testnet hay mainnet

## Chính sách công bố có trách nhiệm

- Chúng tôi xác nhận báo cáo trong vòng **48 giờ**
- Vấn đề critical được fix trong vòng **7 ngày**
- Researcher được credit (nếu đồng ý) trong các thông báo bảo mật
- Không có hành động pháp lý với researcher thiện chí

## Ngoài phạm vi

- Lỗi frontend/UI (báo cáo riêng)
- Dependency bên thứ ba (Uniswap v4, OpenZeppelin)
- Vấn đề đã biết (xem [Bảo mật](../contracts/security.md))
- Social engineering

## Tiếp theo

- [Bảo mật](../contracts/security.md) — kết quả audit và kiến trúc an toàn
- [Tổng quan contracts](../contracts/overview.md) — mọi địa chỉ contract
