# Glossary

20 thuật ngữ cốt lõi, sắp theo thứ tự ABC. Định nghĩa ngắn, ví dụ cụ thể.

### AMM (Automated Market Maker)

Pool thanh khoản dùng công thức toán để định giá swap, thay vì sổ lệnh. PrediX dùng AMM Uniswap v4 cho pool YES-USDC và NO-USDC.

### Arbitrage

Giao dịch không rủi ro dựa trên chênh lệch giá. Ví dụ: YES + NO > $1 → split USDC, bán cả hai, ăn spread.

### CLOB (Central Limit Order Book)

Sổ lệnh giới hạn. Maker đặt giá mua/bán, taker nhận lệnh. PrediX triển khai CLOB on-chain trong contract `PrediXExchange` với 99 tick ($0.01 - $0.99).

### Diamond (EIP-2535)

Proxy pattern cho phép 1 contract chia thành nhiều facet, upgrade từng phần. PrediX dùng Diamond để vượt 24KB bytecode limit của contract đơn.

### Dynamic fee

Phí thay đổi theo điều kiện thị trường. Trên PrediX, phí AMM tăng dần khi gần endTime (0.5% → 5%) để bảo vệ LP khỏi toxic flow.

### Endtime

Thời điểm đóng trading của market. Sau endTime, trading dừng, oracle window mở. Chi tiết: [Resolution](../khai-niem/resolution.md).

### Event (multi-outcome)

Container chứa N market con với constraint mutual-exclusive — đúng 1 member resolve YES=true. Ví dụ: "Ai thắng FIFA WC 2026?" với 48 member là 48 đội.

### Faucet

Dispenser cấp token test miễn phí cho user testnet. PrediX faucet cho 0.0005 ETH + 10.000 USDC / 24h / ví.

### Gauge voting

Mechanism vePRX vote phân bổ LP subsidy cho market. Mô hình từ Curve (veCRV). Chi tiết: [vePRX & gauge](../kinh-te/veprx-gauge.md).

### Hook (Uniswap v4)

Smart contract plug vào Uniswap v4 pool với callback (`beforeSwap`, `beforeAddLiquidity`, v.v.). PrediX Hook apply dynamic fee + anti-sandwich identity verify.

### Invariant

Bất biến hard-enforce on-chain. PrediX có 7 invariant critical (INV-1 đến INV-7). Vi phạm = revert. Xem [Bảo mật](../giao-thuc/bao-mat.md).

### LP (Liquidity Provider)

Người deposit cặp token vào pool AMM để nhận fee swap. PrediX LP cung cấp USDC + YES/NO vào Uniswap v4 pool.

### Market

Đơn vị giao dịch của PrediX. Mỗi market có 1 câu hỏi YES/NO, cặp token outcome, oracle, và endTime.

### Merge

Burn 1 YES + 1 NO → 1 USDC. Ngược với split. Không phí, chỉ gas. Dùng arbitrage khi YES + NO < $1 hoặc rút vị thế đa chiều.

### Multisig

Ví cần N of M signature để thực thi. PrediX dùng 3/5 admin multisig + 2/3 operator multisig trên mainnet.

### Oracle

Nguồn report kết quả sự kiện on-chain. PrediX hỗ trợ Manual, Chainlink, UMA, Committee. Xem [Oracle](../giao-thuc/oracle.md).

### Outcome token

ERC-20 đại diện 1 phía của market (YES hoặc NO). 1 YES đúng = $1, 1 YES sai = $0. Composable với DeFi stack.

### Paymaster (ERC-4337)

Contract tài trợ gas cho UserOp. PrediX Paymaster sponsor gas cho smart account user đã SIWE. Địa chỉ: [addresses](../giao-thuc/addresses.md).

### Permit2

Contract Uniswap cho phép approve ERC-20 1 lần duy nhất rồi sign offline cho từng tx sau. PrediX Router dùng Permit2 để pull USDC gasless approve.

### Prediction market

Sàn giao dịch token phản ánh xác suất sự kiện. Giá YES = $0.68 → thị trường pricing sự kiện 68% khả năng xảy ra.

### PRX

Token governance + doanh thu của PrediX. Total supply 1B, chưa TGE (dự kiến Q4 2026). Chi tiết: [PRX tokenomics](../kinh-te/README.md).

### Redemption

Đổi token thắng thành USDC sau khi market resolve. 1 YES thắng → 1 USDC trừ fee (cap 15%).

### Refund mode

Trạng thái emergency khi oracle không resolve được. User burn cặp YES+NO → nhận lại USDC. Xem [Redeem & refund](../huong-dan/redeem-va-claim.md).

### Router (PrediXRouter)

Stateless aggregator — single entry cho mọi swap. Tự split lệnh giữa CLOB và AMM để tối ưu giá. Bất biến `balanceOf(router) == 0` sau mỗi call.

### SIWE (Sign-In With Ethereum)

Chuẩn EIP-4361 cho đăng nhập web bằng ví. User ký message, BE verify, mint session. PrediX dùng SIWE cho auth.

### Slippage

Chênh lệch giữa giá preview và giá thực tế khi tx execute. Default tolerance 0.5%. Vượt → tx revert.

### Smart account (ERC-4337)

Contract wallet cho phép batch tx, paymaster, passkey. PrediX dùng Kernel v3 smart account với validator passkey/ECDSA.

### Split

Mint 1 YES + 1 NO từ 1 USDC. Ngược với merge. Dùng khi muốn market-make hoặc arbitrage.

### stkPRX

Token non-transferable mint khi stake PRX. Claim share 50% phí protocol bằng USDC. Chi tiết: [Staking real yield](../kinh-te/staking-real-yield.md).

### Timelock

Contract delay thực thi admin action (48h trên PrediX). Diamond upgrade, hook upgrade đều đi qua timelock. Chống admin rugpull.

### Tick (CLOB)

Mức giá rời rạc trong sổ lệnh. PrediX dùng 99 tick ($0.01 - $0.99), tick size $0.01. Không hỗ trợ giá lẻ hơn.

### TVL (Total Value Locked)

Tổng USDC đang lock trong protocol (collateral market + LP pool + staker). Metric phổ biến đo scale DeFi protocol.

### UMA oracle

Permissionless oracle với 48h dispute window + DVM (Data Verification Mechanism) vote khi bị challenge. Phase 2 PrediX.

### USDC

USD Coin, stablecoin 1:1 USD, 6 decimals. PrediX dùng USDC làm collateral duy nhất cho mọi market (testnet: test-USDC).

### vePRX

Vote-escrowed PRX. Lock PRX → nhận vePRX (governance weight + boost yield). Weight giảm tuyến tính theo thời gian.

### YES / NO (outcome token)

Xem [Outcome token](../khai-niem/outcome-tokens.md).
