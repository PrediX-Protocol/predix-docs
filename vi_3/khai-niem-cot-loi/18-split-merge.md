# 18. Split & Merge

### Split: $1 USDC → 1 YES + 1 NO

Gửi USDC vào Diamond contract. Nhận lại 1 YES token + 1 NO token. USDC được giữ làm collateral. Tổng cung YES = NO = totalCollateral tăng tương ứng.

### Merge: 1 YES + 1 NO → $1 USDC

Trả 1 YES + 1 NO cho Diamond. Nhận lại $1 USDC. Tokens bị burn. Tổng cung giảm.

### Tại Sao Quan Trọng?

* **Neo giá:** YES + NO ≈ $1 nhờ arbitrage
* **Tạo thanh khoản:** LP cần YES tokens → split trước
* **Chiến lược:** Mua YES thông qua split + bán NO (hoặc ngược lại)

### Group Split/Merge (Categories)

* **GroupSplit:** Gửi USDC, nhận token cho TẤT CẢ outcomes trong category
* **GroupMerge:** Trả bộ đầy đủ tokens, nhận USDC
