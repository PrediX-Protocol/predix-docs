# Glossary

Thuật ngữ cốt lõi sắp theo ABC. Định nghĩa ngắn + ví dụ.

### AA (Account Abstraction)

ERC-4337 standard cho smart contract wallet. PrediX dùng Kernel v3 smart account với **passkey validator** (sign bằng sinh trắc học). Smart account trả gas qua paymaster — PrediX có chương trình sponsor gas cho user đủ điều kiện (áp dụng cả 2 account types, xem [Paymaster](#paymaster-erc-4337)).

### AMM (Automated Market Maker)

Pool liquidity dùng công thức toán định giá swap. PrediX dùng Uniswap v4 cho pool YES-USDC và NO-USDC.

### Arbitrage

Giao dịch không rủi ro dựa chênh lệch giá. Ví dụ: YES + NO > $1 → split USDC, sell cả 2.

### Badge

NFT earn khi đạt milestone (volume, accuracy, streak). Display trên profile + leaderboard.

### Bond

Stake collateral để propose oracle resolution (UMA) hoặc tạo market (Phase 3). Slash nếu sai.

### Bridge

Chuyển token giữa các chain. PrediX tích hợp Across, Stargate, LayerZero.

### Bundler

ERC-4337 service collect UserOp và submit on-chain. PrediX dùng Pimlico bundler.

### CLOB (Central Limit Order Book)

Sổ lệnh on-chain. Maker đặt giá, taker khớp. PrediX `PrediXExchange` 99 tick ($0.01-$0.99).

### Cliff

Thời gian không nhận token trong vesting. Team PRX có cliff 12 tháng.

### Concentrated liquidity

LP cung cấp liquidity ở range giá hẹp (vs full range $0.01-$0.99). Earnings cao + IL risk cao.

### Diamond (EIP-2535)

Proxy pattern cho phép 1 contract chia thành nhiều facet. PrediX dùng để vượt 24KB bytecode limit + upgrade từng module.

### EOA (Externally Owned Account)

Wallet truyền thống control bằng private key (MetaMask, Ledger). Đối lập smart account.

### endTime

Thời điểm market đóng trading. Sau endTime, oracle window mở.

### Event (multi-outcome)

Container N market con mutually-exclusive — đúng 1 member YES = true. Ví dụ: "FIFA WC 2026 Winner" với 48 đội.

### Faucet

Dispenser cấp token test free cho user testnet. PrediX faucet: 0.0005 ETH + 10k USDC / 24h / ví.

### Gauge voting

Mechanism vePRX vote phân bổ LP subsidy cho market. Pool được vote nhiều → LP earn nhiều hơn.

### Hook (Uniswap v4)

Smart contract plug vào v4 pool với callback (`beforeSwap`, `beforeAddLiquidity`...). PrediX Hook apply anti-sandwich identity verification.

### Impermanent loss (IL)

LP loss vs hold do AMM rebalance khi giá biến động. Bù bằng fee earned.

### Invariant

Bất biến hard-enforce on-chain. PrediX có 7 invariant critical (INV-1 - INV-7).

### LP (Liquidity Provider)

Cung cấp liquidity vào AMM pool, nhận LP NFT, earn fee swap. PrediX LP cung cấp USDC + YES/NO.

### LP NFT

Token đại diện LP position trên Uniswap v4 (PositionManager).

### Maker

User đặt limit order chờ khớp trên CLOB.

### Market

Đơn vị giao dịch — câu hỏi YES/NO + cặp outcome token + oracle + endTime.

### Match type (CLOB)

3 loại matching trên Exchange: complementary (YES↔YES), mint (BUY+BUY ≥ $1), merge (SELL+SELL ≤ $1).

### Merge

Burn 1 YES + 1 NO → 1 USDC. Ngược split. Free phí, dùng arbitrage hoặc rút vị thế đa chiều.

### Multisig

Wallet cần N of M signature để execute. PrediX dùng 3/5 admin + 2/3 operator + 3/5 oracle reporter.

### Oracle

Nguồn report kết quả sự kiện on-chain. PrediX hỗ trợ Manual, Chainlink, UMA, Committee.

### Outcome token

ERC-20 đại diện 1 phía của market (YES hoặc NO). 1 YES đúng = $1, 1 YES sai = $0. Composable DeFi.

### Passkey

Auth standard WebAuthn dùng sinh trắc học (Touch ID, Face ID). Private key trong Secure Enclave.

### Paymaster (ERC-4337)

Contract trung gian pay gas cho UserOp thay user. Smart account user có thể trả gas qua paymaster bằng nhiều cách (USDC, sponsor, etc). PrediX có **chương trình sponsor gas** cho user đủ điều kiện — **áp dụng cả 2 account types** (smart account: paymaster cover trực tiếp; EOA: rebate/refund off-chain). Tiêu chí (new user, stake holder, campaign event) công bố pre-launch + có thể thay đổi theo governance vote.

### Permit2

Contract Uniswap cho approve ERC-20 1 lần + sign offline cho từng tx sau. PrediX Router dùng Permit2 gasless approve.

### Points

Reward off-chain track activity (trade volume, accuracy, LP, …). Convert sang PRX tại weekly distribution.

### Prediction market

Sàn giao dịch token phản ánh xác suất sự kiện. Giá YES = $0.68 → market pricing 68% khả năng xảy ra.

### PRX

Token governance + revenue share của PrediX. Total supply 1B.

### Quadratic voting

Voting mechanism với power = sqrt(stake) — giảm whale dominance. Phase 2 PrediX.

### Refund mode

State emergency khi oracle fail. User burn cặp YES+NO → nhận USDC pro-rata.

### Reorg (reorganization)

Blockchain re-organize do fork. Indexer Ponder handle automatic.

### Router (PrediXRouter)

Stateless aggregator — single entry mọi swap. Tự split CLOB + AMM. Bất biến `balanceOf == 0` post-call.

### Sequencer

L2 component sequence transactions. Chainlink monitor sequencer uptime cho oracle.

### SIWE (Sign-In With Ethereum)

EIP-4361 standard đăng nhập web bằng ví. User sign message off-chain, BE verify, mint session.

### Slippage

Chênh lệch giá preview vs giá thực tế khi tx execute. Default tolerance 0.5%.

### Smart account (ERC-4337)

Contract wallet support batch tx, paymaster, passkey. PrediX dùng Kernel v3 smart account với passkey validator. Đối lập EOA (externally-owned account) như MetaMask thông thường.

### Split

Mint 1 YES + 1 NO từ 1 USDC. Ngược merge. Dùng market-make hoặc arbitrage.

### Stake

Lock PRX vào vault → nhận stkPRX (non-transferable) + USDC yield share.

### Streak

Chuỗi action liên tiếp. Win streak, daily login streak — earn badge + bonus reward.

### stkPRX

Token non-transferable mint khi stake PRX. Claim share phí protocol bằng USDC.

### Taker

User ăn lệnh thị trường qua CLOB.

### Tick (CLOB)

Mức giá rời rạc trong order book. PrediX 99 tick $0.01-$0.99, tick size $0.01.

### Timelock

Contract delay execute admin action (48h trên PrediX). Diamond + Hook upgrade qua timelock — chống admin rugpull.

### TVL (Total Value Locked)

Tổng USDC lock trong protocol (collateral market + LP + staker).

### TBA (To Be Announced)

Placeholder cho info chưa công bố (date, address, parameter).

### UMA oracle

Permissionless oracle với 48h dispute window + DVM (Data Verification Mechanism). Phase 2 PrediX.

### Unichain

L2 EVM chain built by Uniswap Labs. PrediX hiện deploy trên **Unichain Sepolia testnet** (chain `1301`); **mainnet** (chain `130`) sẽ launch sau external audit hoàn tất.

### USDC

USD Coin, stablecoin 1:1 USD, 6 decimals. PrediX dùng USDC làm collateral duy nhất mọi market.

### UserOp (User Operation)

Pseudo-transaction trong ERC-4337. Smart account submit UserOp qua bundler thay vì tx thường.

### vePRX

Vote-escrowed PRX. Lock PRX → nhận vePRX (governance weight + boost yield). Weight giảm theo thời gian.

### VRF (Verifiable Random Function)

Random number on-chain verifiable. PrediX dùng Chainlink VRF cho reward box.

### Webhook

HTTP callback push event tới URL bạn config. Bot listen via webhook thay vì poll.

### YES / NO

Outcome token cho market. Xem [Outcome token](../khai-niem/outcome-tokens.md).
