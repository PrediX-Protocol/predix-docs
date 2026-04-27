# vePRX & gauge voting

Hệ thống governance theo mô hình Curve (veCRV). Lock PRX → vePRX → vote + boost.

## vePRX là gì

**vePRX** = "vote-escrowed PRX". Lock PRX, nhận vePRX (non-transferable) biểu diễn governance weight.

- **Không nhận thêm token** — chỉ ghi nhận quyền.
- **Weight giảm tuyến tính** theo thời gian — gần hết lock thì weight thấp.
- **Hết lock**: vePRX = 0, PRX unlock rút được.

## Công thức weight

```
vePRX_weight = PRX_locked × (remaining_lock_time / max_lock_time)
max_lock_time = 4 năm (48 tháng)
```

Ví dụ:

| Lock amount | Lock duration | vePRX initial |
|---|---|---|
| 1000 PRX | 4 năm (max) | 1000 |
| 1000 PRX | 2 năm | 500 |
| 1000 PRX | 1 năm | 250 |
| 1000 PRX | 6 tháng | 125 |

Sau 6 tháng pass (lock 4-năm còn 3.5 năm) → weight giảm từ 1000 → 875 vePRX.

Decay tuyến tính theo block. Không step.

## Quyền vePRX

```mermaid
%%{init: {"theme":"base","themeVariables":{"primaryColor":"#1e293b","primaryTextColor":"#f1f5f9","primaryBorderColor":"#475569","lineColor":"#94a3b8","background":"transparent","mainBkg":"#334155","tertiaryColor":"#475569"}}}%%
mindmap
  root((vePRX))
    Gauge voting
      Vote pool nhận LP subsidy
      Weekly epoch
    Protocol params
      Fee rate change
      Oracle approve / revoke
      Market creation bond
    Treasury spend
      Approve grant
      Audit funding
      Initiative budget
    Emergency
      Pause module nếu majority
      Crisis response
```

## Gauge voting — flywheel

Mỗi market có **gauge**. vePRX holder vote gauge nào nhận LP subsidy từ treasury (20% fee budget).

```mermaid
flowchart LR
    Lock[User lock PRX] --> VePRX[vePRX weight]
    VePRX --> Vote[Vote gauge weekly]
    Vote --> Subsidy[Treasury chia LP subsidy<br/>theo % vote]
    Subsidy --> LP[LP earn nhiều hơn]
    LP --> Depth[Pool depth tăng]
    Depth --> Trader[Trader happier]
    Trader --> Volume[Volume tăng]
    Volume --> Fee[Fee tăng]
    Fee --> Yield[stkPRX yield tăng]
    Yield --> Demand[User mua PRX để stake]
    Demand --> Lock

    classDef hot fill:#475569,stroke:#334155,color:#fff,stroke-width:1.5px
    class Volume,Fee,Yield hot
```

Mô hình "vote market" từ Curve / Balancer. PrediX áp dụng cho prediction market — gauge = pool YES-USDC (hoặc NO-USDC) của market cụ thể.

## Ai vote gì

- **LP đang cung cấp liquidity** → vote pool họ LP để nhận subsidy nhiều hơn.
- **Project owner tạo market** → vote market của mình để attract LP + trader.
- **Trader quan tâm market** → vote market đó để depth sâu hơn.

## Bribe market

```mermaid
flowchart TD
    Project(["🏢 Project A muốn pool X nhận subsidy"])
    Project --> S1["Project deposit 10k USDC vào Bribe contract<br/>+ chỉ định gauge X"]
    S1 --> Voter(["👤 vePRX voter thấy bribe"])
    Voter --> S2["Voter vote gauge X<br/>(commit vePRX weight, lock 1 epoch)"]
    S2 --> Epoch[("⏱ Cuối epoch")]
    Epoch --> S3["Treasury subsidy chia theo vote<br/>→ Pool X nhận subsidy → LP happy"]
    Epoch --> S4["Bribe contract chia USDC bribe<br/>pro-rata theo voter weight"]
    S3 --> End(["✅ Pool X depth tăng · LP earn fee + subsidy"])
    S4 --> End2(["✅ Voter nhận USDC bribe"])

    classDef proj fill:#2563eb,stroke:#1d4ed8,color:#fff,stroke-width:2px
    classDef voter fill:#475569,stroke:#334155,color:#fff,stroke-width:1.5px
    classDef tick fill:#52525b,stroke:#3f3f46,color:#fff,stroke-width:1.5px
    classDef step fill:#475569,stroke:#334155,color:#fff,stroke-width:1.5px
    classDef ok fill:#16a34a,stroke:#15803d,color:#fff,stroke-width:2px
    class Project proj
    class Voter voter
    class Epoch tick
    class S1,S2,S3,S4 step
    class End,End2 ok
```

External project trả PRX/USDC cho vePRX holder vote pool của họ. Standard DeFi (Convex/Votium model).

PrediX self-host bribe layer hoặc partner với Votium/Paladin (Phase 2).

## Boost USDC yield

vePRX holder cũng là stkPRX holder → nhận boost yield USDC:

| Lock | Yield boost | vote weight |
|---|---|---|
| No lock | 1.0× | 0 |
| 4 năm lock | 2.5× | 4× |

Lock lâu = yield + voting power cao. Đi cùng nhau.

Chi tiết: [Staking real yield](staking-real-yield.md).

## Restrictions

- **vePRX không transfer** được. Buy PRX → lock → có vePRX.
- **Extend lock**: 1-năm còn 3 tháng, extend thêm 9 tháng → 1 năm mới (reset clock).
- **Early unlock**: Phase 2 cho phép unlock sớm với **50% PRX penalty** đi vào treasury. Hiện tại: hard lock.

## Quyền protocol params

vePRX vote thay đổi:

| Param | Default | Range |
|---|---|---|
| AMM fee tier weights | 0.5/1/2/5% | Adjustable, cap 10% |
| CLOB taker fee | 0-1% | Cap 1% |
| Redemption fee default | 0% | Cap 15% (INV-4 hard) |
| Oracle approve list | … | Add/remove adapter |
| Market creation bond | TBA | 1k-100k PRX |
| Stake unstake cooldown | 7 ngày | 1-30 ngày |
| Buyback frequency | Weekly | Daily-Monthly |

Proposal flow:
1. vePRX holder propose change (cần > 100k vePRX để propose, anti-spam).
2. 7-day discussion window trên forum.
3. 5-day voting period (Snapshot off-chain hoặc on-chain).
4. Quorum: > 4% total vePRX vote.
5. Pass: > 50% YES of votes.
6. Execute: Timelock 48h + multisig execute.

## Treasury spend governance

vePRX vote chi tiêu treasury:

- Audit funding (e.g. $200k for Spearbit).
- Grant cho dev / contributor ($5k-100k typical).
- Marketing initiative.
- Insurance fund top-up.

Tương tự process proposal.

## Quadratic voting (TBA)

Phase 2 sẽ experiment quadratic voting cho proposal lớn:
```
voting_power = sqrt(vePRX_weight)
```

Giảm whale dominance. Chỉ apply cho proposal > $100k spend.

## Risks

| Risk | Mitigation |
|---|---|
| **Vote concentration** (whale lock lớn) | Quadratic voting Phase 2 |
| **Bribe corruption** (voter chọn theo bribe, không merit) | Trade-off của model — accepted (Curve work tốt vẫn) |
| **Low participation** (ít ai vote) | Default fallback (đều các pool) + small PRX emission cho voter |

## Timeline rollout

| Phase | Feature |
|---|---|
| **TGE** | Lock PRX → vePRX. Gauge vote basic. |
| **TGE + 3m** | Fee boost tier kích hoạt. |
| **TGE + 6m** | Bribe market Phase 1. |
| **TGE + 12m** | Protocol params vote. |
| **TGE + 18m** | Treasury spend governance. |
| **TGE + 24m+** | Quadratic voting experiment, cross-chain gov. |

Mọi mốc TGE-relative, exact dates **TBA** sau TGE confirmed.

## API

```
GET  /api/v2/governance/proposals
GET  /api/v2/governance/proposals/:id
GET  /api/v2/governance/users/:address/votes
GET  /api/v2/gauges
GET  /api/v2/gauges/:gaugeId/votes
POST /api/v2/governance/vote  # returns calldata
```

Chi tiết: [Backend API](../developers/api-reference.md#backend-endpoints-v2).
