# Kết nối ví

PrediX có **2 phương pháp đăng nhập**. Cả 2 đều **non-custodial** — không ai (kể cả PrediX) giữ private key của bạn.

## Chọn nhanh

```mermaid
flowchart TD
    Start{"Trải nghiệm bạn muốn?"}
    Start -->|"Đơn giản như app web2<br/>không cần biết crypto"| Passkey["<b>Passkey + Smart Account</b><br/>Touch ID · Face ID · WebAuthn<br/>Tự deploy ERC-4337 account<br/>Không cần install extension"]
    Start -->|"Quen DeFi · muốn full control<br/>tự ký mỗi tx"| Wallet["<b>Crypto wallet — EOA</b><br/>MetaMask · Rainbow · WalletConnect<br/>Self-custody seed phrase BIP-39<br/>Compatible với hardware wallet"]

    classDef pk fill:#dcfce7,stroke:#16a34a,color:#0f172a
    classDef cw fill:#dbeafe,stroke:#2563eb,color:#0f172a
    class Passkey pk
    class Wallet cw
```

## So sánh

| | Passkey + Smart Account | Crypto wallet (EOA) |
|---|---|---|
| **Trải nghiệm** | Web2-like, sinh trắc học | Web3 truyền thống, ký mỗi tx |
| **Cài extension** | Không | Có (MetaMask, Rainbow…) |
| **Backup recovery** | Cloud sync (iCloud / Google), hoặc thiết bị thứ 2 | Seed phrase BIP-39 (12-24 từ) |
| **Hardware wallet** | Không (private key trong Secure Enclave) | Có (Ledger, Trezor) |
| **Phí gas** | Tự trả qua paymaster (sponsor cho user đủ điều kiện chương trình) | Tự trả ETH (Unichain rẻ ~$0.001-0.01/tx) |
| **Batch transaction** | Có (1-click `[approve, swap]` atomic) | Không (2 tx riêng) |
| **Đăng ký lần đầu** | ~5 giây sinh trắc học | Đã có ví → instant |
| **Phù hợp** | User mới · onboarding nhanh | DeFi user · custody lớn · hardware wallet |

> **Ghi chú gas**: Mặc định cả 2 phương pháp user đều trả gas. Smart account user có thể nhận **gas sponsor từ PrediX paymaster** nếu đủ điều kiện chương trình (vd: new user onboarding, stake holder ngưỡng X, campaign-eligible event). Tiêu chí + duration của sponsor program công bố pre-launch + có thể thay đổi theo governance vote. EOA user luôn tự trả ETH (Unichain rất rẻ).

## Passkey + Smart Account

Passkey dùng chuẩn **WebAuthn** — sinh trắc học (Touch ID, Face ID, Windows Hello) hoặc PIN của thiết bị xác thực. Private key sinh ra và lưu trong **Secure Enclave / TPM**, không export được.

Khi sign-up, app tự deploy một **Kernel smart account (ERC-4337)** — wallet contract on-chain validator bằng passkey signature. Mọi action đi qua UserOp.

### Tạo mới

1. Mở [app.predix.app](https://app.predix.app) → **Sign up**.
2. Chọn **Continue with passkey**.
3. Browser hỏi xác thực sinh trắc học. Confirm.
4. Smart account counterfactual address xuất hiện ngay (deploy on-chain ở action đầu tiên).

### Backup

- **iCloud Keychain** (iPhone, Mac) — passkey sync giữa các thiết bị Apple cùng Apple ID.
- **Google Password Manager** (Android, Chrome) — sync giữa devices.
- **Hardware key** (YubiKey, Titan) — passkey lưu trên hardware key, dùng plug-in xác thực.

> **Cảnh báo**: Nếu chỉ có 1 thiết bị + không sync cloud + không có hardware key backup → mất thiết bị = mất ví. Khuyến nghị enable cloud sync hoặc thêm thiết bị thứ 2 ngay sau sign-up.

### Recovery (mất thiết bị)

Hiện tại: tạo lại passkey trên thiết bị mới qua cloud sync (nếu enabled).

Roadmap: **Social recovery** với guardian — chỉ định N người tin cậy, M trong N có thể restore quyền truy cập (TBA).

## Crypto wallet (EOA)

Dùng wallet truyền thống bạn đã có — MetaMask, Rainbow, Coinbase Wallet, hoặc bất kỳ wallet nào hỗ trợ WalletConnect / hardware wallet.

### Bước

1. Click **Connect wallet** ở header.
2. Chọn wallet (MetaMask / Rainbow / WalletConnect / Ledger…).
3. Approve connection request trong wallet.
4. Ký **SIWE message** (Sign-In With Ethereum) — off-chain, không tốn gas.
5. Trade trực tiếp — mỗi tx tự trả gas ETH.

### Khi nào nên dùng

- Bạn đã có DeFi workflow với MetaMask + hardware wallet.
- Custody số dư lớn — muốn seed phrase backup chuẩn BIP-39.
- Tích hợp tooling khác (Frame, Rabby, Safe multisig).
- Preferred full control, không muốn dependency vào paymaster.

## SIWE session

Cả 2 phương pháp đều dùng **SIWE** (EIP-4361) để tạo session với backend:

```mermaid
sequenceDiagram
    participant U as User
    participant W as Ví / Passkey
    participant FE as PrediX FE
    participant BE as PrediX BE

    U->>FE: Click "Sign in"
    FE->>BE: GET /auth/challenge?address=0x...
    BE-->>FE: { message, nonce }
    FE->>W: signMessage(message)
    W-->>FE: signature
    FE->>BE: POST /auth/verify { address, signature }
    BE-->>FE: Set-Cookie: predix_session
    Note over FE,BE: Session 7 ngày
```

Session lưu HTTPOnly cookie, không expose JS. Hết hạn → re-sign.

## Permissions cần biết

Khi connect, app request:
- **eth_requestAccounts** — đọc address.
- **personal_sign** — ký SIWE (off-chain, không tốn gas).
- **eth_sendTransaction** hoặc UserOp — execute tx.

App **không bao giờ** request `eth_signTypedData` cho approve token với amount lớn không cần thiết — luôn permit2 với deadline ngắn.

## Đăng xuất

- App: nút **Disconnect** ở header → xoá session cookie.
- Crypto wallet: ngắt kết nối từ wallet UI.
- Passkey smart account: chỉ "log out" session — smart account vẫn tồn tại on-chain, lần sau sign-in lại dùng cùng address.
