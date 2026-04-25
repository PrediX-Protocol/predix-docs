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
sequenceDiagram
    participant Creator
    participant Oracle as ChainlinkOracle
    participant CL as Chainlink Aggregator
    participant Anyone
    participant Diamond

    Creator->>Oracle: register(marketId, feed, threshold, gte, snapshotAt)
    Note over Oracle: feed = BTC/USD<br/>threshold = $100k<br/>snapshotAt = endTime
    Note over Anyone,CL: ...endTime trôi qua...
    Anyone->>Oracle: resolve(marketId, roundIdHint, prevHint)
    Oracle->>CL: getRoundData(roundIdHint)
    CL-->>Oracle: { answer, updatedAt }
    Oracle->>Oracle: Verify updatedAt ≥ snapshotAt
    Oracle->>Oracle: Verify prev.updatedAt < snapshotAt
    Oracle->>Oracle: Verify L2 sequencer uptime
    Oracle->>Diamond: outcome = (answer ≥ threshold) == gte
    Diamond-->>Anyone: MarketResolved event
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
sequenceDiagram
    participant E as Event happen
    participant Multisig
    participant Oracle as ManualOracle
    participant Diamond
    participant User

    E-->>Multisig: Event resolves (e.g. Argentina wins)
    Multisig->>Multisig: Verify từ ≥ 2 sources
    Multisig->>Oracle: report(marketId, outcome=true) [3/5 sign]
    Oracle-->>User: OracleReportCreated event
    User->>Diamond: resolveMarket(marketId)
    Diamond->>Oracle: read outcome
    Diamond->>Diamond: Set isResolved=true, outcome=true
    Diamond-->>User: MarketResolved event
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
sequenceDiagram
    participant P as Proposer
    participant U as UMA Oracle
    participant D as Disputer
    participant DVM as UMA DVM
    participant M as Market

    P->>U: propose(outcome, bond)
    Note over U: 48h dispute window
    alt Không ai dispute
        U-->>P: Refund bond + finalize outcome
        U->>M: resolveMarket(outcome)
    else Có dispute
        D->>U: dispute(bond)
        U->>DVM: vote
        DVM-->>U: Decision
        Note over P,D: Loser lose bond
        U->>M: resolveMarket(final outcome)
    end
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
