# FAQ

## Sản phẩm

### Prediction market là gì?

Sàn giao dịch token phản ánh xác suất một sự kiện sẽ xảy ra. Mỗi sự kiện có 2 token: YES (sự kiện xảy ra) và NO (không xảy ra). Khi resolve, token đúng = $1 USDC, token sai = $0.

Giá token giữa $0 và $1 = xác suất thị trường đánh giá. YES = $0.68 nghĩa là thị trường đang pricing sự kiện 68% khả năng xảy ra.

### Tại sao giá YES + NO luôn ≈ $1? {#yes-no-1-dollar}

Vì có thể **split** 1 USDC → mint ra 1 YES + 1 NO. Nếu YES + NO > $1, arbitrageur sẽ split USDC và bán cả hai để kiếm lời, đẩy giá xuống. Nếu < $1, mua cả hai và merge để kiếm lời, đẩy giá lên. Cân bằng ở ~$1.

### PrediX khác Polymarket ở đâu?

| | PrediX | Polymarket |
|---|---|---|
| Token outcome | ERC-20 (composable) | ERC-1155 |
| Thanh khoản | CLOB + AMM hybrid | CLOB only |
| Chain | Unichain | Polygon |
| Phi tập trung | Non-custodial, on-chain | Non-custodial nhưng CLOB matching off-chain |
| Access | Non-US được hỗ trợ, APAC-first | Bị chặn 33+ quốc gia |

### Non-US có dùng được không?

Có. PrediX không có geo-block mặc định. Tuy nhiên tuỳ khu vực bạn ở, luật địa phương có thể quy định khác. Tự kiểm tra trước khi dùng.

## Ví & tài khoản

### Passkey vs MetaMask — chọn gì? {#passkey-vs-metamask}

- **Passkey**: Tiện, không cài extension, sinh trắc học. Nhưng nếu mất thiết bị duy nhất có passkey → mất ví. Phù hợp số dư nhỏ, onboard nhanh.
- **MetaMask (EOA)**: Khôi phục được bằng seed phrase. Phù hợp số dư lớn.
- **MetaMask + smart account**: Tốt nhất cả hai — seed phrase backup + batch tx gasless.

### Mất thiết bị có passkey, làm sao?

Testnet: tạo passkey mới, claim faucet lại. Mainnet sẽ có social recovery (guardian-based). Đang trong lộ trình, xem [changelog](../tai-nguyen/changelog.md).

### Ví có thể xuất seed không?

EOA (MetaMask) — có, như bình thường. Smart account tạo từ passkey — không. Private key nằm trong Secure Enclave của thiết bị, không exportable. Nếu cần backup, dùng MetaMask + smart account thay vì passkey.

## Gas & phí

### Tôi có phải trả gas không?

Nếu dùng smart account (passkey hoặc MetaMask upgraded), paymaster PrediX tài trợ gas cho các action chính: swap, split, merge, redeem, place/cancel limit order.

EOA trực tiếp thì tự trả. ETH Sepolia lấy từ faucet của PrediX (0.0005 ETH / 24h).

### Phí huỷ lệnh CLOB? {#phi-huy-lenh}

Không phí protocol. Chỉ trả gas nếu dùng EOA. Smart account → free qua paymaster.

### Phí maker/taker là bao nhiêu?

Maker (đặt lệnh limit chờ khớp): **0%**.
Taker (ăn lệnh thị trường trên CLOB): **0% cho đến Mainnet + 7 ngày**, sau đó 0–1% dynamic.
AMM swap: **0.5–5%** tuỳ thời gian tới endTime (gần endTime phí cao hơn để bảo vệ LP).

Chi tiết: [Cấu trúc phí](../khai-niem/phi.md).

## Faucet & testnet

### Làm sao lấy USDC test? {#lay-usdc-test}

1. Kết nối ví.
2. Lần đầu, UI sẽ hỏi claim faucet. Nhấn **Claim**.
3. Nhận 0.0005 ETH + 10.000 test-USDC sau ~5 giây.
4. Cooldown: 1 lần / 24 giờ / ví.

Test-USDC không có giá trị. Đừng OTC.

### State testnet có reset không?

Có, khi chuyển sang mainnet. Chi tiết trong [changelog](../tai-nguyen/changelog.md) khi mainnet announce.

## Bảo mật

### Contract đã được audit chưa?

Đang trong audit bên ngoài. Các finding internal đã được fix, 5 blocker còn open đang xử lý. Chưa khuyến nghị deploy mainnet tới khi audit xong.

Chi tiết: [Bảo mật & timelock](../giao-thuc/bao-mat.md).

### Admin có thể rugpull không?

Diamond upgrade qua `diamondCut` phải đi qua TimelockController 48h. Hook upgrade qua proxy cũng có timelock 48h monotonic (không thể giảm xuống dưới 48h). Community có 48h để detect + react (withdraw, fork).

Xem thêm: [Bảo mật & timelock](../giao-thuc/bao-mat.md).

## Hỗ trợ

### Báo bug ở đâu?

- Bug vô hại: GitHub issue.
- Bug bảo mật: bug bounty theo mức severity, email [security@predix.app](mailto:security@predix.app) (xem [Liên kết](../tai-nguyen/links.md)).

### Community?

- Discord: [xem links](../tai-nguyen/links.md)
- Twitter/X: cùng link
