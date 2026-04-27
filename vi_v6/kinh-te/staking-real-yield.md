# Staking real yield

Lock PRX → nhận share **50% phí protocol** dạng **USDC thật**. Không emission.

## Cơ chế

```mermaid
flowchart TD
    Start(["👤 User deposit N PRX vào Staking Vault"])
    Start --> S1["Vault mint N stkPRX cho user<br/>(non-transferable, đại diện share)"]
    S1 --> Loop[("⏱ Mỗi week (epoch):<br/>Protocol collect 50% fee USDC<br/>distribute vault pro-rata")]
    Loop --> S2["Vault account share user theo stake"]
    S2 --> Claim(["👤 User gọi claim()"])
    Claim --> End(["✅ Vault transfer USDC yield về ví user"])

    classDef st fill:#dbeafe,stroke:#2563eb,color:#0f172a
    classDef step fill:#fef3c7,stroke:#d97706,color:#0f172a
    classDef loop fill:#f1f5f9,stroke:#64748b,color:#0f172a
    classDef ok fill:#dcfce7,stroke:#16a34a,color:#0f172a
    class Start,Claim st
    class S1,S2 step
    class Loop loop
    class End ok
```

1. Deposit N PRX vào `StakingVault`.
2. Vault mint N **stkPRX** (non-transferable) cho bạn — claim share fee.
3. Mỗi week (epoch), protocol collect 50% fee USDC từ AMM + CLOB + redemption + creation → distribute vault pro-rata.
4. Claim USDC bất cứ lúc nào, hoặc auto-compound (convert PRX rồi re-stake).
5. Unstake: cooldown 7 ngày (chống front-run khi event lớn).

## Tính yield

```
staker_share = 50% × total_fees_USDC_weekly
your_yield   = staker_share × (your_stake / total_staked)
APY_USDC     = (weekly_yield × 52) / your_stake_USD_value
```

### Ví dụ steady state

- Protocol volume: $200M/tháng
- Fee mix: 0.3% → revenue $600k/tháng
- Staker portion: $300k/tháng = $3.6M/năm USDC
- Total stake: 100M PRX @ $0.05 = $5M market cap staked
- **APY_USDC = 3.6M / 5M = 72%**

Yield float theo volume thật. Volume tăng → yield tăng. Volume giảm → yield giảm.

## So với staking model khác

| Protocol | Yield source | Sustainability |
|---|---|---|
| **Lido stETH** | ETH staking yield (~3%) | Cao |
| **Aave stkAAVE** | Emission stkAAVE + safety module | Trung bình (inflationary) |
| **GMX esGMX** | Fee USDC + ETH từ volume | Cao (real yield) |
| **PrediX stkPRX** | **Fee USDC từ volume protocol** | **Cao (real yield, GMX model)** |

PrediX chọn GMX model:
- Không dilute supply PRX.
- Yield gắn với business performance, không ép emission.
- User đo được: volume lên → yield lên.

## Lock boost

Lock PRX để nhận **boost yield + governance weight**:

```mermaid
flowchart LR
    Stake[Stake không lock<br/>stkPRX] -->|1.0× yield| Y0[USDC yield base]
    Lock3[Lock 3m] -->|1.1×| Y1[USDC yield × 1.1]
    Lock6[Lock 6m] -->|1.25×| Y2[USDC yield × 1.25]
    Lock12[Lock 12m] -->|1.5×| Y3[USDC yield × 1.5]
    Lock24[Lock 24m] -->|2.0×| Y4[USDC yield × 2.0]
    Lock48[Lock 48m max] -->|2.5×| Y5[USDC yield × 2.5]

    classDef low fill:#fef3c7,stroke:#d97706,color:#0f172a
    classDef high fill:#dcfce7,stroke:#16a34a,color:#0f172a
    class Stake,Y0 low
    class Lock48,Y5 high
```

| Lock | Yield boost | vePRX weight |
|---|---|---|
| No lock (stkPRX) | 1.0× | 0 |
| 3 tháng | 1.1× | 0.25× |
| 6 tháng | 1.25× | 0.5× |
| 12 tháng | 1.5× | 1.0× |
| 24 tháng | 2.0× | 2.0× |
| 48 tháng (max) | 2.5× | 4.0× |

Boost từ treasury (20% phí). Lock càng lâu = yield USDC + governance power càng cao.

Chi tiết governance: [vePRX & gauge](veprx-gauge.md).

## Fee discount cho staker

Stake ≥ ngưỡng → giảm fee giao dịch:

| Stake threshold | CLOB taker discount | AMM discount |
|---|---|---|
| 1,000 PRX | 10% | 0% |
| 10,000 PRX | 25% | 10% |
| 100,000 PRX | 50% | 25% |
| 1,000,000 PRX | 50% (cap) | 50% (cap) |

Discount active ngay khi stake, apply tự động trong mỗi tx.

## Rủi ro staker

| Risk | Mitigation |
|---|---|
| **Volume giảm** → yield giảm | Diversify nhiều income stream (LP, content) |
| **PRX price giảm** → USD value yield giảm | Stake để long-term, không trade short-term |
| **Smart contract risk** | Vault audit cùng core protocol. Bug bounty active |
| **Slashing** | Vault staking **không** bị slash (chỉ oracle dispute bond Phase 2 mới có slash) |

## Unstake flow

```mermaid
stateDiagram-v2
    [*] --> Staked
    Staked --> CooldownRequested : Request unstake
    CooldownRequested --> Cooldown : 7 ngày bắt đầu
    Cooldown --> Cooldown : Vẫn nhận yield
    Cooldown --> Withdrawable : 7 ngày elapsed
    Withdrawable --> [*] : Claim PRX
```

1. UI: **Request unstake**.
2. Cooldown 7 ngày bắt đầu. Trong cooldown:
   - Vẫn nhận yield (không rug bạn giữa chừng).
   - Không rút được PRX.
   - Không cancel được (tránh flip-flop).
3. Sau 7 ngày: **Claim** → PRX về ví.

Cooldown tránh user unstake trước event lớn rồi restake ngay sau (giảm tính ổn định yield pool).

## Auto-compound

Nếu enable auto-compound:
- Mỗi epoch yield USDC tự động:
  1. Buy PRX trên thị trường (qua Router).
  2. Re-stake vào vault.
- Compound interest effect.

Off mặc định — bạn opt-in trong vault settings.

## Khi nào staking khởi động

- Launch cùng TGE (TBA).
- Có thể có period **pre-stake** 1-2 tuần cho whitelist (pre-seed, seed, team) để bootstrap.
- Staking contract deploy + audit song song core, expect cùng mainnet.

## Insurance fund (Phase 2)

Dự kiến: 5% staker yield → insurance fund treasury. Mục đích: backup nếu protocol bị exploit, partial reimbursement cho user affected. Detail TBA.

## API

```
GET  /api/v2/staking/stats           # total staked, APR, your share
GET  /api/v2/staking/:address        # your stake, unclaimed yield
POST /api/v2/staking/:address/claim  # trigger claim (returns calldata)
GET  /api/v2/staking/history         # epoch distribution history
```

Chi tiết: [Backend API](../developers/backend-api.md).
