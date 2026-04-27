# Oracle

Oracle là layer report kết quả sự kiện on-chain. PrediX dùng pluggable architecture — nhiều loại oracle cùng tồn tại.

## Vấn đề cốt lõi

Smart contract không thể tự biết:
- "Argentina đã thắng FIFA WC chưa?"
- "BTC giá $100k đã vượt chưa?"
- "Bầu cử Mỹ 2028 ai thắng?"

Cần **nguồn ngoài** đưa data on-chain. Oracle sai = market resolve sai = user mất tiền.

## 4 phases, 4 loại oracle

```mermaid
flowchart TB
    Q{Loại sự kiện?}
    Q -->|Price threshold<br/>BTC > $100k?| Chain[ChainlinkOracle<br/>Auto resolve, permissionless]
    Q -->|Subjective<br/>Ai thắng bầu cử?| Manual[ManualOracle<br/>Multisig 3/5]
    Q -->|Decentralized<br/>không muốn multisig| UMA[UMAOracle<br/>48h dispute window<br/>Phase 2 TBA]
    Q -->|Cross-chain<br/>governance vote| Committee[Committee oracle<br/>t-of-N validator<br/>Phase 3 TBA]

    classDef p1 fill:#dcfce7,stroke:#16a34a,color:#0f172a
    classDef p2 fill:#fef3c7,stroke:#d97706,color:#0f172a
    classDef p3 fill:#dbeafe,stroke:#2563eb,color:#0f172a
    class Chain,Manual p1
    class UMA p2
    class Committee p3
```

## ChainlinkOracle

Tự động, permissionless.

```mermaid
flowchart TD
    Creator(["🛠️ Creator: register(marketId, feed, threshold, gte, snapshotAt)<br/>feed=BTC/USD · threshold=$100k · snapshotAt=endTime"])
    Creator --> Wait[("⏱ ...endTime trôi qua...")]
    Wait --> Anyone(["👤 Anyone: resolve(marketId, roundIdHint, prevHint)"])
    Anyone --> S1["Oracle gọi Chainlink.getRoundData(roundIdHint)<br/>→ { answer, updatedAt }"]
    S1 --> Check["Oracle verify (3 conditions):<br/>• updatedAt ≥ snapshotAt<br/>• prev.updatedAt &lt; snapshotAt (adjacency)<br/>• L2 sequencer uptime OK"]
    Check --> S2["Compute outcome = (answer ≥ threshold) == gte"]
    S2 --> End(["✅ Diamond emit MarketResolved event"])

    classDef st fill:#dbeafe,stroke:#2563eb,color:#0f172a
    classDef step fill:#fef3c7,stroke:#d97706,color:#0f172a
    classDef wait fill:#f1f5f9,stroke:#64748b,color:#0f172a
    classDef ok fill:#dcfce7,stroke:#16a34a,color:#0f172a
    class Creator,Anyone st
    class S1,Check,S2 step
    class Wait wait
    class End ok
```

**Use case**: Price-threshold market (BTC, ETH, asset prices, FX rates).

**Strict checks**:
- Round adjacency — đảm bảo dùng đúng round at-snapshot, không stale, không skip.
- L2 sequencer uptime — không resolve trong grace period sau outage (avoid bad data).
- Feed staleness threshold — reject nếu round quá cũ vs realtime.

## ManualOracle

Multisig 3/5 đọc kết quả từ nguồn off-chain, ký tx.

### Sources accept

| Loại event | Source acceptable |
|---|---|
| Sports | Official league API, Reuters, AP |
| Election | Election commission official, AP, Reuters |
| Crypto event | On-chain data, official project announcement |
| Weather | NOAA, regional met office |
| Award show | Official website |

### Flow

```mermaid
flowchart TD
    Event(["📰 Event resolves real-world<br/>(e.g. Argentina wins WC)"])
    Event --> S1["Multisig verify từ ≥ 2 sources<br/>(Reuters, AP, official API)"]
    S1 --> S2["Multisig 3/5 sign<br/>ManualOracle.report(marketId, outcome=true)"]
    S2 --> S3(["📢 Emit OracleReportCreated event"])
    S3 --> S4["Anyone gọi Diamond.resolveMarket(marketId)"]
    S4 --> S5["Diamond đọc outcome từ oracle<br/>Set isResolved=true, outcome=true"]
    S5 --> End(["✅ Emit MarketResolved · user redeem được"])

    classDef event fill:#dbeafe,stroke:#2563eb,color:#0f172a
    classDef step fill:#fef3c7,stroke:#d97706,color:#0f172a
    classDef ok fill:#dcfce7,stroke:#16a34a,color:#0f172a
    class Event event
    class S1,S2,S4,S5 step
    class S3,End ok
```

### Risks + mitigation

| Risk | Mitigation |
|---|---|
| **Multisig compromise** (3/5 keys stolen) | HSM signer, role separation, geographic distribution, key rotation 6 tháng |
| **Bribery** (whale bribe multisig) | Public audit trail per signature, reputation stake (Phase 2), refund mode escape hatch |
| **Operator honest mistake** | Social challenge window, multisig discuss before sign, refund mode revert option |
| **Coordinated capture** | Long-term: phase out manual cho UMA + committee oracle |

### Revoke case

Admin có thể `revoke(marketId)` clear pending report khi:
- Report set sai do bug.
- Có dispute cần re-examine.

**Cảnh báo**: revoke **không revert** `isResolved`. Chỉ clear pending **trước** khi resolve. Sau resolve, không revoke được (immutable invariant INV-6).

## UMAOracle (Phase 2 — TBA)

Permissionless propose + 48h dispute window.

```mermaid
flowchart TD
    Start(["👤 Proposer: propose(outcome, bond)"])
    Start --> Wait[("⏱ 48h dispute window")]
    Wait --> Branch{"Có ai dispute?"}
    Branch -->|Không| OK1["UMA refund bond cho proposer<br/>+ finalize outcome"]
    Branch -->|Có| Disp["👤 Disputer post bond"]
    Disp --> Vote["UMA DVM vote<br/>quyết định outcome final"]
    Vote --> Slash["Loser lose bond<br/>winner take"]
    OK1 --> End(["✅ Market.resolveMarket(outcome)"])
    Slash --> End

    classDef st fill:#dbeafe,stroke:#2563eb,color:#0f172a
    classDef step fill:#fef3c7,stroke:#d97706,color:#0f172a
    classDef wait fill:#f1f5f9,stroke:#64748b,color:#0f172a
    classDef bad fill:#fee2e2,stroke:#dc2626,color:#0f172a
    classDef ok fill:#dcfce7,stroke:#16a34a,color:#0f172a
    class Start st
    class Wait wait
    class Branch,OK1 step
    class Disp,Vote,Slash bad
    class End ok
```

### Bond sizing

```
bond = max(min_bond, min(market_tvl × 0.5%, max_bond))
min_bond = $500 USDC
max_bond = $50,000 USDC
```

Bond scale với market size → disincentive spam, align incentive.

### Use case

Sự kiện cần decentralized resolution, không phụ thuộc multisig.

## Committee oracle (Phase 3 — TBA)

- **t-of-N threshold signature** (e.g. 5-of-9 validator).
- **Commit-reveal voting** prevent front-run.
- **Slashing** PRX nếu vote sai vs final consensus.
- **Stake PRX** để làm validator.
- **Cross-chain** support qua Wormhole / LayerZero.

### Use case

Cross-chain governance outcome, complex composite event.

## So sánh oracle types

| | Manual | Chainlink | UMA | Committee |
|---|---|---|---|---|
| Ai resolve | Multisig 3/5 | Anyone | Anyone propose, DVM dispute | t-of-N validator |
| Subjective event | ✅ | ❌ | ✅ | ✅ |
| Dispute mechanism | Off-chain social | Không (data is law) | On-chain 48h | On-chain commit-reveal |
| Latency | Tức thì sau ký | ~30s (1 round) | 48h default | Sau commit-reveal cycle |
| Decentralization | Thấp | Trung bình | Cao | Cao |
| Bond required | Không | Không | Có | Stake validator |

## Refund mode — last resort

Khi không oracle nào resolve được:

```mermaid
flowchart LR
    Fail[Oracle fail<br/>feed down · multisig không respond ·<br/>UMA dispute hung]
    Fail --> Admin[Admin propose<br/>enableRefundMode]
    Admin --> Time[TimelockController 48h delay]
    Time --> Active[refundModeActive = true]
    Active --> User[User refund: burn min YES+NO<br/>→ nhận USDC pro-rata]

    classDef warn fill:#fee2e2,stroke:#dc2626,color:#0f172a
    classDef wait fill:#fef3c7,stroke:#d97706,color:#0f172a
    classDef ok fill:#dcfce7,stroke:#16a34a,color:#0f172a
    class Fail warn
    class Time wait
    class Active,User ok
```

Detail: [Redeem & refund](../huong-dan/redeem-va-claim.md).

## Resolve sai — flow handle

| Phase | Mechanism |
|---|---|
| **Phase 1 Manual** | Multisig discuss, social consensus → enable refund mode nếu xác định sai |
| **Phase 2 UMA** | Dispute qua UMA, DVM final |
| **Mọi phase** | Không bao giờ revert `isResolved=true` (INV-6 hard) |

## Oracle approval list

Diamond giữ set oracle approved:

- `approveOracle(addr)` — admin add adapter mới (instant).
- `revokeOracle(addr)` — admin remove (instant) — chỉ ngừng dùng cho market **mới**.
- Market đã create với oracle đó **vẫn dùng được** oracle đó — tránh retroactive break.

## Oracle selection per market

| Loại event | Khuyến nghị |
|---|---|
| Price threshold (BTC > $100k) | ChainlinkOracle |
| Sport / election | ManualOracle (Phase 1) → UMAOracle (Phase 2+) |
| On-chain event (gov vote, TVL) | Custom adapter qua Diamond approve |
| Subjective (debate winner) | UMAOracle |
| Complex composite | Custom adapter hoặc manual + committee |

Chi tiết market creation: [Tạo market](../huong-dan/tao-market.md).
