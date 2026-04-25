# FAQ

## Sản phẩm

### Prediction market là gì?

Sàn giao dịch token phản ánh xác suất một sự kiện xảy ra. Mỗi sự kiện có 2 token: YES và NO. Khi resolve, token đúng = $1 USDC, sai = $0.

Giá YES = $0.68 → thị trường đang pricing sự kiện 68% khả năng xảy ra.

### Tại sao YES + NO luôn ≈ $1? {#yes-no-1-dollar}

Vì có thể **split** 1 USDC → mint 1 YES + 1 NO bất cứ lúc nào (atomic on-chain).

- YES + NO > $1 → arbitrageur split USDC, sell cả hai → ăn spread → đẩy giá xuống.
- YES + NO < $1 → arbitrageur mua cả hai, merge → ăn spread → đẩy giá lên.

Cân bằng tự động ở ~$1 (chênh lệch nhỏ chỉ vì AMM curve + spread).

### PrediX khác Polymarket ở đâu?

| | PrediX | Polymarket |
|---|---|---|
| Outcome token | ERC-20 (composable) | ERC-1155 |
| Liquidity | CLOB + AMM hybrid | CLOB only (matching off-chain) |
| Chain | Unichain (L2) | Polygon |
| Account | Passkey + smart account (AA) hoặc EOA wallet | EOA + Magic Link |
| Geo restriction | Per regulation từng region, không default block | Cấm 33+ quốc gia |
| Token economy | PRX với real yield + buyback-burn | UMA staking, no native token |

### Có thể trade từ đâu?

PrediX là protocol on-chain phi tập trung. Truy cập từ bất kỳ đâu có internet. Tuỳ region bạn ở, **luật địa phương** có thể quy định khác — tự kiểm tra. PrediX UI áp dụng geo-restriction theo compliance từng jurisdiction.

## Ví & tài khoản

### Passkey vs crypto wallet, chọn cái nào? {#passkey-vs-metamask}

PrediX có 2 phương pháp:

- **Passkey + Smart Account** (ERC-4337): Tiện nhất, UX web2-like, không cài extension. Sinh trắc học (Touch ID / Face ID) mở khoá. Backup qua cloud sync hoặc thiết bị thứ 2. Hiện được paymaster sponsor gas. Phù hợp user mới, số dư nhỏ-vừa.
- **Crypto wallet (EOA)** — MetaMask, Rainbow, Coinbase Wallet, WalletConnect, Ledger: Khôi phục bằng seed phrase BIP-39, compatible hardware wallet. Tự trả gas ETH. Phù hợp DeFi user quen, custody lớn, hoặc tích hợp tooling khác.

Cả 2 đều **non-custodial** — PrediX không bao giờ giữ private key.

### Mất thiết bị có passkey thì sao? {#mat-passkey}

- Có cloud sync (iCloud / Google Password) → install lại app trên thiết bị mới, đăng nhập cloud, passkey tự sync.
- Có thiết bị thứ 2 đã sync passkey → mở app trên thiết bị đó.
- Không có gì cả → mất ví. Phase 2 sẽ có **social recovery** (guardian-based).

**Tip**: Setup 2 thiết bị có passkey từ đầu, hoặc enable cloud sync.

### Ví passkey có xuất seed được không?

Không. Private key của passkey nằm trong Secure Enclave / TPM, không exportable theo design. Nếu cần seed phrase backup chuẩn BIP-39, dùng **crypto wallet (EOA)** thay vì passkey.

### Smart account address của tôi là gì?

Sau khi sign-in lần đầu, app hiển thị address. Đây là **counterfactual address** — đã có dù chưa deploy tx nào on-chain. Mọi nạp tiền vào address này đều an toàn.

## Gas & phí

### Có phải trả gas không? {#gas-co-mat-tien}

- **Passkey + Smart Account**: Hiện tại paymaster PrediX sponsor gas cho các action chính (swap, split, merge, redeem, place/cancel order). **Free cho user trong giai đoạn bootstrap** — chính sách paymaster có thể thay đổi tương lai (theo governance vote).
- **Crypto wallet (EOA)**: Tự trả gas bằng ETH Unichain. Gas Unichain rất rẻ — thường $0.001-0.01 per tx.

### Phí maker/taker bao nhiêu?

- **Maker** (đặt limit order chờ khớp): **0%** luôn.
- **Taker** (ăn lệnh thị trường qua CLOB): 0-1% dynamic theo thời gian tới endTime.
- **AMM swap**: 0.5-5% dynamic. Gần endTime fee cao hơn để bảo vệ LP khỏi toxic flow.

Chi tiết: [Cấu trúc fee](../khai-niem/phi.md).

### Phí cancel limit order?

Không phí protocol. Smart account user → gas paymaster sponsor (hiện free). EOA → trả gas ETH thường.

### Phí redeem khi resolve?

0-1.5% (cap on-chain 15%). Snapshot tại creation của market — admin tăng fee không apply cho market đã có. Default thường 0%.

## Funding

### Nạp USDC từ Binance / Coinbase được không? {#nap-tu-cex}

Có. Nhiều CEX đã add Unichain network — withdraw trực tiếp.

Nếu CEX bạn dùng chưa add: withdraw qua Ethereum hoặc Arbitrum, rồi dùng [Bridge widget](bridge.md) trong app. Coinbase, Binance, OKX, Bybit hầu hết đều support Unichain hoặc các chain bridge dễ.

### Bridge có an toàn không?

PrediX tích hợp các bridge có TVL hàng tỷ USD: Across, Stargate, LayerZero. Tất cả đã audit nhiều round.

**Lưu ý**: Cross-chain bridge là vector tấn công lớn nhất trong DeFi history. Chỉ bridge amount cần thiết, không hold dài hạn trên bridge. Test với amount nhỏ trước khi bridge lớn.

### Nạp tối thiểu bao nhiêu?

- App không enforce minimum.
- Tuy nhiên: <$5 thì gas + phí bridge có thể chiếm tỷ lệ lớn so với amount.
- Khuyến nghị: $20+ để hiệu quả về phí.

## Trading

### Slippage là gì?

Chênh lệch giữa giá preview và giá thực tế khi tx execute. Default tolerance 0.5%.

Vượt slippage → tx **revert**, tiền không mất (EOA tốn gas ETH; smart account hiện được paymaster sponsor).

### Trade nhỏ nhất bao nhiêu?

- Market order: $1 USDC tối thiểu.
- Limit order: $1 USDC để tránh dust orders.
- Không có max — limit chỉ là liquidity của pool/CLOB.

### Có hỗ trợ stop-loss / OCO?

Phase 2. Hiện chỉ market + limit thông thường.

### Slippage cao nghĩa là gì?

Market thiếu liquidity. Có 3 lựa chọn:
1. Giảm size lệnh.
2. Đặt limit order chờ khớp.
3. Trở thành LP để earn fee từ chính trade của mình.

## Resolve & redeem

### Bao lâu market resolve?

- **Chainlink oracle**: Tự động sau snapshotAt + 1 round Chainlink (~30 giây - 1 phút).
- **Manual oracle**: Multisig 3/5 ký, thường trong vòng 24h.
- **UMA oracle**: 48h dispute window + DVM nếu dispute.

### Phải redeem trong bao lâu?

Không bắt buộc, nhưng có **grace 365 ngày**. Sau đó admin có thể `sweepUnclaimed` thu hồi token chưa claim về treasury.

### Market không resolve được thì sao?

Admin enable **refund mode** qua timelock 48h. User burn cặp YES+NO nhận lại USDC pro-rata.

## Bảo mật

### Contract đã audit chưa?

Có. Audit firm + report public ở [Bảo mật & timelock](../giao-thuc/bao-mat.md). Ngoài ra có bug bounty range $100 - $500k tuỳ severity.

### Admin có thể rugpull?

Không. Diamond upgrade qua TimelockController **48h delay**. Hook upgrade qua proxy 48h timelock self-gated monotonic. Community có 48h để verify, react (withdraw, fork) trước khi bất kỳ change nào execute.

`CUT_EXECUTOR_ROLE` chỉ thuộc về TimelockController contract — không EOA nào bypass được.

### Có insurance fund không?

Phase 2 (TBA). Hiện protocol chưa có insurance pool.

### PrediX có giữ tiền user không?

Không. Router stateless — `balanceOf(router) == 0` enforce on-chain sau mỗi tx. Tiền user nằm trong:
- Ví user (USDC, YES, NO tokens)
- Diamond contract (collateral lock cho market)
- LP position (nếu cung cấp liquidity)
- Stake vault (nếu stake PRX)

## PRX token

### TGE khi nào?

TBA. Theo dõi [Discord / Twitter chính thức](../tai-nguyen/links.md) cho announcement.

### Stake PRX để làm gì?

Nhận share 50% phí protocol bằng **USDC thật**, vote governance, fee discount. Chi tiết: [Staking real yield](../kinh-te/staking-real-yield.md).

### vePRX khác stkPRX thế nào?

- **stkPRX**: stake không lock, nhận yield USDC.
- **vePRX**: stake + lock 1-48 tháng, nhận **boost yield** + governance weight (gauge voting).

Càng lock lâu càng nhiều yield + voting power. [vePRX & gauge](../kinh-te/veprx-gauge.md).

### Airdrop?

Có testnet points campaign convert sang PRX tại TGE. Tỷ lệ công bố pre-TGE qua kênh chính thức.

## Hỗ trợ

### Báo bug ở đâu?

- **Bug bảo mật** (smart contract, exploit): [security@predix.app](mailto:security@predix.app), bug bounty payout theo severity.
- **Bug UI / app**: GitHub issue hoặc Discord #bug-report.

### Hỏi cộng đồng?

Discord, Twitter, Telegram — xem [Liên kết](../tai-nguyen/links.md).
