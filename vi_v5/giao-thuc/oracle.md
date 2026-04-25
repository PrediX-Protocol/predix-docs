# Oracle

Oracle là layer report kết quả sự kiện on-chain. PrediX dùng pluggable architecture — nhiều loại oracle cùng tồn tại.

## Vấn đề cốt lõi

Smart contract không thể tự biết:
- "Argentina đã thắng FIFA WC chưa?"
- "BTC giá $100k đã vượt chưa?"
- "Bầu cử Mỹ 2028 ai thắng?"

Cần **nguồn ngoài** (oracle) đưa data vào. Oracle chọn sai = market resolve sai = user mất tiền.

## 4 phases, 4 loại oracle

### Phase 1 (hiện tại — testnet + mainnet Q2 2026)

**ManualOracle** + **ChainlinkOracle**.

Bình thường ưu tiên Chainlink nếu event có feed on-chain. Manual fallback cho sự kiện subjective.

### Phase 2 (Q3 2026)

Thêm **UMAOracle** — permissionless proposal + 48h dispute window.

### Phase 3 (2027+)

**Committee oracle** — t-of-N threshold sig, commit-reveal vote, slashing mechanism.

### Phase 4 (roadmap)

Cross-chain oracle (Wormhole / LayerZero) cho multi-chain market.

## ManualOracle — Phase 1

**Contract**: `ManualOracle`, address testnet `0x7887f07AF62CE0a4Cf836136135a61b59c36A9d2`.

### Flow

1. Market tạo ra, gán `oracle = ManualOracle.address`.
2. Event xảy ra, endTime qua.
3. Admin multisig 3/5 đọc kết quả từ nguồn thực tế:
   - Báo chí uy tín (Reuters, AP, local news).
   - On-chain data (nếu có — ví dụ kết quả smart contract khác).
   - Video / streaming (cho sports, debate).
4. Multisig ký `ManualOracle.setOutcome(marketId, outcome)`.
5. `MarketFacet.resolveMarket(marketId)` đọc outcome từ oracle → set market state.

### Invariants

- **INV-6**: Sau `setOutcome`, không ai revert được outcome (hard check).
- Audit trail: event `OracleReportCreated(marketId, outcome, reporter)` emit on-chain, indexer vào bảng `manualOracleReport`.

### Rủi ro

- **Multisig compromise**: nếu 3/5 key bị chiếm → fake outcome. Mitigate: HSM signer, role separation, key rotation định kỳ.
- **Bribery**: whale bribe multisig. Mitigate: reputation staking (Phase 2), public audit trail.
- **Operator honest mistake**: đọc sai nguồn. Mitigate: social challenge window + refund mode escape hatch.

### Revoke case

Admin có thể `revoke(marketId)` delete pending report. Điều này dùng khi:
- Report set sai do bug.
- Có dispute cần re-examine.

**Cảnh báo**: revoke không revert `isResolved`. Chỉ clear pending report trước khi resolve. Sau resolve, không revoke được (H-10 open concern, đang xử lý).

## ChainlinkOracle — Phase 1 Chainlink

**Contract**: `ChainlinkOracle`. Sẽ enable trên mainnet — testnet Unichain Sepolia chưa có feed Chainlink (`CHAINLINK_ENABLED=false`).

### Flow

1. Market tạo ra, gán `oracle = ChainlinkOracle.address`.
2. Creator gọi `register(marketId, feed, threshold, gte, snapshotAt)`:
   - `feed` = Chainlink AggregatorV3 address (ví dụ BTC/USD).
   - `threshold` = giá target (6 decimals).
   - `gte` = `true` nếu YES khi price ≥ threshold, `false` nếu ngược lại.
   - `snapshotAt` = timestamp quyết định (thường = endTime).
3. Sau endTime, **anyone** gọi `resolve(marketId, roundIdHint)` với round id gần snapshotAt.
4. Contract verify:
   - `chainlinkFeed.getRoundData(roundIdHint).updatedAt >= snapshotAt`.
   - `previousRound.updatedAt < snapshotAt` (adjacency — FIN-02 fix).
   - L2 sequencer uptime feed OK (grace period 1h sau outage).
5. Outcome = `price >= threshold` (hoặc `<=` nếu `gte=false`).

### Ưu điểm

- **Permissionless resolve** — ai cũng gọi được, không chờ admin.
- **Verifiable** — data từ Chainlink, transparent.
- **Fast** — resolve ngay sau snapshotAt + 1 round.

### Giới hạn

- **Chỉ cho price-threshold** market. Không dùng được cho subjective event.
- **Phụ thuộc Chainlink uptime**. Mainnet Ethereum / Arbitrum / Optimism có feed ổn định. Unichain đang TBD.
- **Manipulation window**: nếu ai control > 50% hash rate CEX contribute feed → price có thể bị pump/dump. Mitigate: `snapshotAt` cố định, không moving window.

## UMAOracle — Phase 2

**Deploy target**: Q3 2026, sau mainnet launch.

### Flow

1. Market tạo, oracle = `UMAOracle`.
2. Sau endTime, anyone propose outcome + post bond (USDC).
3. 48h dispute window:
   - Nếu không ai dispute → outcome finalized, proposer lấy lại bond.
   - Nếu có dispute → UMA DVM (Data Verification Mechanism) vote. Loser lose bond.
4. Outcome được `MarketFacet.resolveMarket(marketId)` đọc.

### Bond sizing

```
bond_amount = max(min_bond, min(market_tvl × 0.5%, max_bond))
min_bond = $500 USDC
max_bond = $50,000 USDC
```

Bond scale với market size → disincentive spam dispute, align incentive.

### So sánh

| | ManualOracle | ChainlinkOracle | UMAOracle |
|---|---|---|---|
| Ai resolve | Multisig 3/5 | Anyone (verify on-chain data) | Anyone propose, UMA DVM fallback |
| Subjective event | ✅ | ❌ | ✅ |
| Dispute | Social, off-chain | Không có (data là law) | On-chain, 48h, DVM vote |
| Latency | Tức thì (multisig sign) | 1 Chainlink round | 48h default |
| Decentralization | Low (multisig) | Medium (Chainlink) | High (UMA DVM) |

## Committee oracle — Phase 3

Roadmap:
- **t-of-N threshold signature** (ví dụ 5-of-9 validator).
- **Commit-reveal voting** — prevent front-run vote.
- **Slashing** nếu validator vote sai vs final consensus.
- **Stake PRX** để làm validator → align incentive.

## Oracle approval list

Contract `Diamond` giữ set các oracle được approve:

- `approveOracle(addr)` — admin add adapter mới.
- `revokeOracle(addr)` — admin remove adapter (ngừng dùng cho market mới).
- Market đã create với oracle đó **vẫn dùng được** oracle đó — tránh retroactive break.

## Refund mode — last resort

Nếu **không** oracle nào resolve được (feed chết, multisig không respond, UMA dispute hung):

1. Admin + TimelockController 48h call `enableRefundMode(marketId)`.
2. User gọi `refund(marketId)` với cặp YES+NO → USDC pro-rata.

Chi tiết: [Resolution & oracle](../khai-niem/resolution.md).

## Oracle selection per market

Khi tạo market, creator chọn oracle dựa:

| Loại sự kiện | Oracle khuyến nghị |
|---|---|
| Price threshold (BTC > $100k) | ChainlinkOracle |
| Kết quả sports / bầu cử | ManualOracle (Phase 1) → UMAOracle (Phase 2+) |
| On-chain event (governance vote pass, protocol TVL) | Custom adapter |
| Subjective (ai thắng debate) | UMAOracle |
| Complex (composite) | Custom adapter hoặc manual + committee |
