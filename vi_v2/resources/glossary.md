# Bảng thuật ngữ

## Giao thức

| Thuật ngữ | Định nghĩa |
|------|-----------|
| **Prediction Market** | Thị trường nơi người tham gia giao dịch dựa trên kết quả sự kiện tương lai |
| **Outcome Token** | Token ERC-20 đại diện cho một kết quả cụ thể (YES hoặc NO) |
| **YES Token** | Trả $1 USDC nếu sự kiện xảy ra; $0 nếu không |
| **NO Token** | Trả $1 USDC nếu sự kiện không xảy ra; $0 nếu có |
| **Split** | Đổi USDC thành cặp YES + NO (1 USDC → 1 YES + 1 NO) |
| **Merge** | Đổi cặp YES + NO về USDC (1 YES + 1 NO → 1 USDC) |
| **Redeem** | Đổi token thắng lấy $1 USDC mỗi token sau khi resolve |
| **Category** | Nhóm các market nhị phân liên quan cho sự kiện nhiều kết quả |

## Kiến trúc

| Thuật ngữ | Định nghĩa |
|------|-----------|
| **CLOB** | Central Limit Order Book — engine khớp lệnh on-chain |
| **AMM** | Automated Market Maker — pool thanh khoản theo thuật toán (Uniswap v4) |
| **Smart Router** | Gộp thanh khoản CLOB và AMM để khớp tối ưu |
| **Hook** | Contract tuỳ biến của Uniswap v4 chạy tại các điểm vòng đời pool |
| **Diamond Proxy** | Pattern EIP-2535 cho phép smart contract modular, có thể nâng cấp |
| **Facet** | Module trong Diamond proxy chứa các hàm liên quan |
| **Oracle** | Contract bên ngoài xác định kết quả market |

## DeFi

| Thuật ngữ | Định nghĩa |
|------|-----------|
| **TVL** | Total Value Locked — tổng USDC collateral trong giao thức |
| **Slippage** | Chênh lệch giữa giá kỳ vọng và giá thực thi |
| **Spread** | Chênh lệch giữa bid tốt nhất và ask tốt nhất |
| **Depth** | Tổng khối lượng lệnh tại một mức giá |
| **IL** | Impermanent Loss — giá trị LP bị lỗ do biến động giá |

## Chuẩn token

| Thuật ngữ | Định nghĩa |
|------|-----------|
| **USDC** | USD Coin — stablecoin dùng làm collateral (6 decimals) |
| **ERC-20** | Chuẩn fungible token trên EVM |
| **ERC-2612** | Extension Permit cho phép approve không tốn gas qua chữ ký |
| **ERC-1155** | Chuẩn multi-token (Polymarket dùng, không composable với DeFi) |
