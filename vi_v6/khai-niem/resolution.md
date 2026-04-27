# Resolution & oracle

Một market cần nguồn *sự thật* để quyết định YES hay NO thắng. Nguồn đó là **oracle**.

## Lifecycle market

```mermaid
stateDiagram-v2
    [*] --> Created : createMarket()
    Created --> Trading : market mở
    Trading --> EndTime : tới endTime
    EndTime --> Resolved : oracle.resolve()
    Resolved --> Redemption : user claim 1:1 USDC
    EndTime --> RefundMode : oracle fail / dispute
    RefundMode --> Refunded : user merge YES+NO
    Redemption --> [*]
    Refunded --> [*]
```

| Stage | Mô tả |
|---|---|
| **Created** | Creator (có `CREATOR_ROLE`) tạo market, đặt `endTime` + chọn oracle |
| **Trading** | User split / merge / trade tới `endTime` |
| **EndTime** | Trading đóng (hook chặn add liquidity + swap). Oracle window mở |
| **Resolved** | Oracle gọi `resolveMarket()` với outcome |
| **Redemption** | User giữ token đúng → redeem 1:1 USDC trừ fee |
| **RefundMode** | Fallback nếu oracle không resolve được (oracle down, dispute hung) |
| **Refunded** | User burn cặp YES+NO → nhận USDC pro-rata |

## 4 loại oracle

```mermaid
flowchart TB
    Q{Loại sự kiện?}
    Q -->|Price threshold<br/>BTC > $100k?| Chain[ChainlinkOracle<br/>Auto resolve, permissionless]
    Q -->|Subjective<br/>Ai thắng bầu cử?| Manual[ManualOracle<br/>Multisig 3/5]
    Q -->|Decentralized<br/>không muốn multisig| UMA[UMAOracle<br/>48h dispute window]
    Q -->|Cross-chain<br/>governance vote| Committee[Committee oracle<br/>t-of-N validator]

    classDef p1 fill:#dcfce7,stroke:#16a34a,color:#0f172a
    classDef p2 fill:#fef3c7,stroke:#d97706,color:#0f172a
    classDef p3 fill:#dbeafe,stroke:#2563eb,color:#0f172a
    class Chain,Manual p1
    class UMA p2
    class Committee p3
```

### ChainlinkOracle

Tự động, permissionless.

```mermaid
flowchart TD
    Creator(["🛠️ Creator register oracle<br/>(marketId, feed=BTC/USD, threshold=$100k, gte, snapshotAt=endTime)"])
    Creator --> Wait[("⏱ ...endTime trôi qua...")]
    Wait --> Anyone(["👤 Anyone gọi resolve(marketId, roundIdHint, prevHint)"])
    Anyone --> S1["Oracle.getRoundData(roundIdHint) từ Chainlink<br/>→ { answer, updatedAt }"]
    S1 --> Check["Oracle verify:<br/>• updatedAt ≥ snapshotAt<br/>• prev.updatedAt &lt; snapshotAt (adjacency)<br/>• L2 sequencer uptime OK"]
    Check --> S2["Compute outcome<br/>= (answer ≥ threshold) == gte"]
    S2 --> End(["✅ Diamond.MarketResolved event<br/>Market resolve, user redeem được"])

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

### ManualOracle

Multisig 3/5 đọc kết quả từ nguồn off-chain, ký tx.

- Source: Reuters, AP, official API, on-chain data, video streaming.
- Outcome **immutable** sau khi set (invariant INV-6).
- Audit trail: `OracleReportCreated` event on-chain.

**Use case**: Sự kiện subjective (sport, election, debate winner).

**Risk mitigation**:
- HSM signer + role separation
- Public audit trail per signature
- Refund mode escape hatch nếu phát hiện sai sau

### UMAOracle (Phase 2 — TBA)

Permissionless propose + 48h dispute window.

```mermaid
flowchart TD
    Start(["👤 Proposer: propose(outcome, bond)"])
    Start --> Wait[("⏱ 48h dispute window")]
    Wait --> Branch{"Có ai dispute?"}
    Branch -->|Không| OK1["UMA refund bond cho proposer<br/>+ finalize outcome"]
    Branch -->|Có| Disp["Disputer post bond<br/>UMA DVM vote"]
    Disp --> Decide["DVM quyết định<br/>(loser lose bond, winner take)"]
    OK1 --> End(["✅ Market.resolveMarket(outcome)"])
    Decide --> End

    classDef st fill:#dbeafe,stroke:#2563eb,color:#0f172a
    classDef step fill:#fef3c7,stroke:#d97706,color:#0f172a
    classDef wait fill:#f1f5f9,stroke:#64748b,color:#0f172a
    classDef bad fill:#fee2e2,stroke:#dc2626,color:#0f172a
    classDef ok fill:#dcfce7,stroke:#16a34a,color:#0f172a
    class Start st
    class Wait wait
    class Branch,OK1 step
    class Disp,Decide bad
    class End ok
```

**Bond sizing**: `max(min_bond, min(market_tvl × 0.5%, max_bond))`. Range $500 - $50,000 USDC.

**Use case**: Sự kiện cần decentralized resolution, không phụ thuộc multisig.

### Committee oracle (Phase 3 — TBA)

- t-of-N threshold signature (e.g. 5-of-9 validator).
- Commit-reveal voting prevent front-run.
- Slashing PRX nếu vote sai vs final consensus.
- Cross-chain via Wormhole / LayerZero.

**Use case**: Cross-chain governance outcome, complex composite event.

## So sánh

| | Manual | Chainlink | UMA | Committee |
|---|---|---|---|---|
| Ai resolve | Multisig 3/5 | Anyone | Anyone propose, DVM dispute | t-of-N validator |
| Subjective event | ✅ | ❌ | ✅ | ✅ |
| Dispute | Off-chain social | Không (data is law) | On-chain 48h | On-chain commit-reveal |
| Latency | Tức thì sau ký | Sau 1 round (~30s-1min) | 48h default | Sau commit-reveal cycle |
| Decentralization | Thấp | Trung bình | Cao | Cao |

## Refund mode — last resort

Khi không oracle nào resolve được:

```mermaid
flowchart LR
    Fail[Oracle fail<br/>feed down, multisig không respond,<br/>UMA dispute hung]
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

## Resolve sai — làm gì

| Phase | Cơ chế |
|---|---|
| **Phase 1** (Manual) | Multisig discuss, social consensus. Nếu đa số đồng ý sai → enable refund mode |
| **Phase 2** (UMA) | Dispute qua UMA protocol, DVM vote |
| **Mọi phase** | Không bao giờ revert `isResolved=true` (INV-6 hard) |

## Ai có thể tạo market

- **Phase 1**: Address có `CREATOR_ROLE` (admin + whitelist creator).
- **Phase 3 (TBA)**: Permissionless — ai cũng tạo được nếu stake **bond PRX** (10k PRX proposed). Bond slash nếu market malformed hoặc resolve dispute.

Chi tiết: [Tạo market](../huong-dan/tao-market.md).

## Timing

- **Resolve window**: 7 ngày sau endTime. Chậm hơn → có thể trigger refund mode default.
- **Redemption**: Không deadline cứng, grace 365 ngày, sau đó admin có thể `sweepUnclaimed` về treasury.
- **UMA dispute**: 48h sau propose.

Time params config per-market hoặc global. Xem [Smart contracts](../giao-thuc/smart-contracts.md).
