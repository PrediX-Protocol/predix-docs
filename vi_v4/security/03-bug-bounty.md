---
description: Bug bounty program + responsible disclosure process.
---

# Bug Bounty & Disclosure

{% hint style="warning" %}
**Bug bounty sẽ go-live sau external audit + mainnet launch**. Trong giai đoạn testnet, report qua email `security@predixpro.io` vẫn được nhận + đánh giá; reward discretionary.
{% endhint %}

## Program (mainnet, TBA)

- **Platform**: Sherlock hoặc Immunefi (sẽ công bố trước mainnet)
- **Max payout**: TBD — dự kiến tier với critical up to $100K–$250K
- **Scope**: Tất cả 6 packages SC + Indexer + BE
- **Launch**: Align với mainnet deploy

## Tier (dự kiến)

| Severity | Reward range (testnet) | Reward range (mainnet TBA) |
|---|---|---|
| Critical | Discretionary | $50K–$250K |
| High | Discretionary | $10K–$50K |
| Medium | Discretionary | $2K–$10K |
| Low | Discretionary | $500–$2K |
| Informational | Discretionary | $0–$500 |

### Severity definition

**Critical**: Drain funds, freeze funds, bypass invariants INV-1/2/3/6/7.
- Example: Mint YES token without collateral
- Example: Redeem more than `payout = amount × (1 − fee)`
- Example: Router stuck > 0 USDC after call

**High**: Partial fund loss, DoS of critical path, bypass INV-4/5, unauthorized admin action.
- Example: Sandwich qua Hook không có commit
- Example: Fee > 15% cap
- Example: Non-CUT_EXECUTOR call `diamondCut`

**Medium**: Minor fund leak (dust), incorrect accounting, recoverable DoS.
- Example: PnL calculation off by a small amount
- Example: User orders miscategorized

**Low**: UX / correctness issues không affect money.
- Example: Response envelope drift
- Example: Event indexed incorrectly

**Informational**: Best practice, gas optimization, code quality.

## In-scope

### Smart contracts
- Tất cả contract deployed trên Unichain (testnet hiện tại, mainnet khi launch)
- Source code trong `SC/packages/{shared,oracle,diamond,hook,exchange,router}`
- Deployed bytecode khớp source (verify qua explorer)

### Off-chain
- Indexer (`INDEXER/`) — bugs ảnh hưởng accounting
- Backend API (`BE/`) — bugs ảnh hưởng auth, data integrity
- Frontend (`FE_new/`) — bugs ảnh hưởng user funds (vd transaction signing hijack)

## Out-of-scope

- **Informational issues** without security impact (gas golf, style)
- **DoS on public RPC** (not our infrastructure)
- **Phishing sites** imitating PrediX — report to domain registrar + email us
- **User's wallet compromise** (private key leak, hardware wallet physical attack)
- **Social engineering** against team
- **Best-practice recommendations** không có exploit path
- **3rd-party dependencies** (Uniswap v4, Chainlink, OpenZeppelin) — report directly to those projects
- **Test / mock contracts** in `test/` folders

## Responsible disclosure process

### Step 1 — Email (PGP-encrypted khuyến khích)

```
To: security@predixpro.io
Subject: [SECURITY] <short description>
```

Body nên include:

- **Severity estimate** (critical / high / medium / low)
- **Summary** — 1-2 sentences
- **Reproduction** — step-by-step với code hoặc tx hash
- **Impact** — ai bị affect, mức độ
- **Suggested fix** (optional)
- **Your handle** — để credit trong hall of fame (nếu đồng ý)

### Step 2 — Acknowledge (24h)

Team sẽ respond trong 24h với:

- Confirm receipt
- Assigned ticket ID
- Preliminary severity classification
- Timeline estimate

### Step 3 — Investigation (1-7 days)

- Team reproduce + verify
- Draft remediation
- Engage reporter cho clarifying Q

### Step 4 — Remediation

- Patch + deploy (testnet trước nếu possible)
- Post-mortem draft
- Share với reporter cho review

### Step 5 — Public disclosure

- Sau patch deployed + confirmed
- Typical embargo: 30 ngày từ patch
- Coordinated post trên blog + Discord + Twitter
- Reporter credit (nếu đồng ý)

## PGP key

```
-----BEGIN PGP PUBLIC KEY BLOCK-----
(TBA — key sẽ publish trước bounty go-live)
-----END PGP PUBLIC KEY BLOCK-----
```

Fingerprint: `TBA`

## Don'ts

1. **KHÔNG** exploit on mainnet để prove concept — dùng testnet hoặc local fork
2. **KHÔNG** publicly disclose trước embargo — forfeit reward
3. **KHÔNG** DoS infrastructure (RPC, API) cho "testing"
4. **KHÔNG** social engineer team members
5. **KHÔNG** claim multiple duplicate reports — chỉ first valid report eligible

## Safe harbor

Team cam kết:

- **Không** pursue legal action chống researcher follow disclosure policy
- **Không** share researcher identity without consent
- **Credit** researcher trong hall of fame (nếu đồng ý)
- **Timely response** + clear communication

## Hall of fame

Danh sách researcher đã contribute security improvements:

| Researcher | Date | Severity | Credit |
|---|---|---|---|
| *(empty — waiting for first valid report)* | | | |

## Contact summary

- **Security email**: `security@predixpro.io` (PGP khuyến khích)
- **Emergency (active exploit)**: `security@predixpro.io` + DM team lead on TG `@predix_security`
- **General questions**: `info@predixpro.io`
