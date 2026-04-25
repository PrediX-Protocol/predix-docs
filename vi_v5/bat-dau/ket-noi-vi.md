# Kết nối ví

PrediX hỗ trợ 3 cách đăng nhập. Chọn theo nhu cầu.

## So sánh nhanh

| Phương thức | Phù hợp với | Cần cài gì | Phí gas |
|---|---|---|---|
| **Passkey** (Touch ID / Face ID) | User mới, không muốn cài extension | Không | Miễn phí qua paymaster |
| **MetaMask / Rainbow** (EOA) | User DeFi quen | Extension ví EVM | Tự trả gas |
| **MetaMask + smart account** | Muốn batch tx, gasless cho một số action | Extension ví EVM | Miễn phí qua paymaster |

## Passkey — nhanh nhất

1. Nhấn **Sign up** → **Continue with passkey**.
2. Trình duyệt / thiết bị hỏi Touch ID hoặc Face ID. Xác nhận.
3. Một smart account (ERC-4337) được tạo tự động. Địa chỉ và private key nằm trong Secure Enclave / TPM của máy, không ai copy được.
4. Nếu mất thiết bị: không khôi phục được — đây là trade-off của passkey. Dùng cho số dư nhỏ, hoặc lưu seed backup bằng cách export sau.

Chi tiết kỹ thuật: smart account dùng Kernel v3 + passkey-validator. Giao dịch gửi qua UserOp, paymaster PrediX tài trợ gas.

## MetaMask EOA

1. Cài MetaMask, thêm Unichain Sepolia:
   - RPC: `https://sepolia.unichain.org`
   - Chain ID: `1301`
   - Symbol: `ETH`
   - Explorer: `https://sepolia.uniscan.xyz`
2. Kết nối qua RainbowKit ở góc phải.
3. Ký message SIWE (Sign-In With Ethereum) để tạo session.

Mỗi tx bạn tự trả gas bằng ETH Sepolia. Dùng [faucet](#faucet) của PrediX để nhận 0.0005 ETH + 10.000 USDC test.

## MetaMask + smart account (AA)

Kết hợp bảo mật của MetaMask (hardware wallet hoặc seed phrase) với tiện ích batch transaction:

1. Kết nối MetaMask bình thường.
2. Nhấn **Upgrade to smart account**. MetaMask ký signature tạo Kernel smart account có EOA của bạn làm validator ECDSA.
3. Các tx sau được paymaster tài trợ gas.

Khác biệt với passkey: signer vẫn là EOA, có thể khôi phục bằng seed. Vẫn hỗ trợ batch `[approve, swap]` thành 1 click.

## Faucet

Lần đầu kết nối, UI sẽ hỏi claim faucet. Mỗi ví được 1 lần mỗi 24 giờ:
- **0.0005 ETH** (Unichain Sepolia) — cho phí gas nếu không dùng paymaster
- **10.000 test-USDC** — để giao dịch

Địa chỉ faucet: `0x7beD6B3D8397Bc9F77626f84D64BED8894C27350`. Faucet do backend relay — bạn không trực tiếp gọi contract.

## Giới hạn testnet

- Không có giá trị thật. Không mua USDC test ngoài chợ.
- Reset định kỳ: khi audit xong và deploy mainnet, state testnet sẽ stop indexing, số dư không migrate.
- Hoạt động testnet được tracked qua **points campaign** — convert sang reward PRX tại TGE theo tỷ lệ công bố trước. Xem [Kinh tế](../kinh-te/README.md).
