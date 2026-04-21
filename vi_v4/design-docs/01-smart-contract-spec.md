---
description: Smart contract specification — state, events, access control, invariants. Input cho auditor engagement.
---

# Smart Contract Specification

**Document status**: v1.0 — 2026-04-17 (testnet deployed)
**Audience**: External audit firms (Spearbit, Trail of Bits, OpenZeppelin, Zellic shortlisted), security researchers, protocol integrators needing deep contract understanding
**Scope**: 6 packages — `shared`, `oracle`, `diamond`, `hook`, `exchange`, `router`

---

## 1. Spec format & conventions

- **State**: diamond storage pattern per module, `keccak256("predix.storage.<module>")` slot
- **Events**: all indexed appropriately, max 3 indexed fields per EIP-712 convention
- **Errors**: custom errors (not `require(string)`), typed — see [Custom Errors Registry](../smart-contracts/custom-errors.md)
- **Access**: role-based via `AccessControlFacet`, roles in `shared/src/constants/Roles.sol`
- **Invariants**: 7 critical, tested via Foundry invariant fuzz — see [Invariants](../security/02-invariants.md)

## 2. Package: `shared`

### 2.1 Constants

**`Roles.sol`**

```solidity
bytes32 constant DEFAULT_ADMIN_ROLE = 0x00;
bytes32 constant ADMIN_ROLE         = keccak256("predix.role.admin");
bytes32 constant OPERATOR_ROLE      = keccak256("predix.role.operator");
bytes32 constant PAUSER_ROLE        = keccak256("predix.role.pauser");
bytes32 constant CUT_EXECUTOR_ROLE  = keccak256("predix.role.cut_executor");
```

**`Modules.sol`** (pause keys)

```solidity
bytes32 constant MARKET  = keccak256("predix.module.market");
bytes32 constant DIAMOND = keccak256("predix.module.diamond");
```

### 2.2 `TransientReentrancyGuard`

EIP-1153 transient storage reentrancy guard. Single slot, shared across Diamond + Router + Exchange.

```solidity
bytes32 private constant _SLOT = bytes32(uint256(keccak256("predix.reentrancy.v1")) - 1);

modifier nonReentrant() {
    assembly {
        if tload(_SLOT) { revert(0, 0) }
        tstore(_SLOT, 1)
    }
    _;
    assembly { tstore(_SLOT, 0) }
}
```

**Audit focus**: Slot collision impossibility (unique hash namespaced per-contract-instance? No — shared global slot via `TRANSIENT` scope. Because `tstore` is tx-scoped, collision within tx means cross-contract reentry same as single-contract, which is desired to block. Verify this is the intended semantic.)

### 2.3 `OutcomeToken` (ERC-20 + Permit)

Minimal ERC-20 + ERC-2612 permit. Deployed as market child by MarketFacet.

**State:**
```solidity
string public name;
string public symbol;
uint8 public constant decimals = 6;
address public immutable factory; // = Diamond address
mapping(address => uint256) public balanceOf;
mapping(address => mapping(address => uint256)) public allowance;
uint256 public totalSupply;
mapping(address => uint256) public nonces; // EIP-2612
```

**Critical functions:**
```solidity
function mint(address to, uint256 amount) external onlyFactory;
function burn(address from, uint256 amount) external onlyFactory;
function permit(owner, spender, value, deadline, v, r, s); // EIP-2612 gasless approval
```

**Invariant**: Only `factory` (Diamond) can mint/burn (INV-7).

## 3. Package: `oracle`

### 3.1 `IOracle` interface

```solidity
interface IOracle {
    function getResolution(uint256 marketId)
        external view returns (bool resolved, bool outcome);
}
```

Đơn giản có chủ ý — mỗi adapter tự handle dispute, staleness, etc. nội bộ.

### 3.2 `ManualOracle`

**State:**
```solidity
address public admin;
mapping(uint256 => bool) private _resolved;
mapping(uint256 => bool) private _outcomes;
mapping(uint256 => uint64) private _resolvedAt;
```

**Functions:**
```solidity
function resolve(uint256 marketId, bool outcome) external onlyAdmin;
function getResolution(uint256 marketId) external view returns (bool, bool);
function getResolvedAt(uint256 marketId) external view returns (uint64);
function setAdmin(address newAdmin) external onlyAdmin;
```

**Events:**
```solidity
event ResolutionSet(uint256 indexed marketId, bool outcome, address indexed operator);
event AdminChanged(address indexed oldAdmin, address indexed newAdmin);
```

**Invariants:**
- INV-6 (monotonic): Once `_resolved[marketId] = true`, cannot revert
- Admin is single-signer — mainnet admin MUST be multisig

**Dispute mechanism (Phase 2 add-on)**: 48h challenge window before resolution becomes "locked" — admin can override in window. See [Oracle Design](02-oracle-design.md).

### 3.3 `ChainlinkOracle`

**State:**
```solidity
struct MarketBinding {
    AggregatorV3Interface priceFeed;
    int256 threshold;
    Comparison cmp; // enum: GT, GTE, LT, LTE
    uint256 resolveTime;
    bool bound;
}
mapping(uint256 => MarketBinding) private _bindings;
mapping(uint256 => bool) private _resolved;
mapping(uint256 => bool) private _outcomes;
AggregatorV3Interface public sequencerUptimeFeed; // L2 sequencer feed
uint256 public constant STALENESS_WINDOW = 24 hours;
uint256 public constant GRACE_PERIOD = 1 hours; // post-sequencer-up grace
```

**Resolve logic:**
```solidity
function resolve(uint256 marketId) external {
    MarketBinding memory b = _bindings[marketId];
    if (!b.bound) revert Oracle_MarketNotBound();
    if (block.timestamp < b.resolveTime) revert Oracle_ResolveTimeNotReached();

    // L2 sequencer uptime check
    (, int256 seqAnswer, uint256 seqStartedAt, , ) = sequencerUptimeFeed.latestRoundData();
    if (seqAnswer == 1) revert Oracle_SequencerDown();
    if (block.timestamp - seqStartedAt < GRACE_PERIOD) revert Oracle_SequencerGracePeriod();

    // Price feed query
    (uint80 roundId, int256 price, , uint256 updatedAt, uint80 answeredInRound) =
        b.priceFeed.latestRoundData();
    if (price <= 0) revert Oracle_InvalidPrice();
    if (block.timestamp - updatedAt > STALENESS_WINDOW) revert Oracle_StalePrice();
    if (answeredInRound < roundId) revert Oracle_RoundIncomplete();

    bool outcome = _evaluate(price, b.threshold, b.cmp);
    _resolved[marketId] = true;
    _outcomes[marketId] = outcome;
    emit Resolved(marketId, price, outcome, roundId);
}
```

**Audit focus**: All Chainlink safety checks applied correctly? Sequencer grace period appropriate for Unichain (L2 built on OP Stack)?

## 4. Package: `diamond`

### 4.1 Storage libraries

All facets read/write via `LibXStorage.layout()` pattern:

| Library | Slot | Purpose |
|---|---|---|
| `LibDiamondStorage` | `predix.storage.diamond` | selector → facet mapping (EIP-2535 core) |
| `LibAccessControlStorage` | `predix.storage.ac` | role members + admin roles |
| `LibPausableStorage` | `predix.storage.pausable` | module → paused bool |
| `LibMarketStorage` | `predix.storage.market` | marketId → MarketData + counters |
| `LibEventStorage` | `predix.storage.event` | eventId → EventGroup |
| `LibConfigStorage` | `predix.storage.config` | approvedOracles, feeRecipient, defaultCaps |

**Append-only guarantee**: Violation bricks upgrade. Audit must verify no reorder/removal in refactors.

### 4.2 `MarketData` struct

```solidity
struct MarketData {
    string question;
    uint256 endTime;
    address yesToken;
    address noToken;
    uint256 eventId;                          // 0 = standalone; != 0 = event child
    address oracle;
    bool isResolved;
    bool outcome;                              // true = YES won
    uint256 totalCollateral;
    uint256 resolvedAt;
    bool refundModeActive;
    uint16 snapshottedRedemptionFeeBps;       // Fee at create (FINAL-H04)
    uint16 perMarketRedemptionFeeBps;
    bool redemptionFeeOverridden;
    uint256 perMarketCap;                      // 0 = default cap
}
```

### 4.3 MarketFacet — full signature

```solidity
interface IMarketFacet {
    // Admin (ADMIN_ROLE)
    function createMarket(string calldata question, uint256 endTime, address oracle)
        external returns (uint256 marketId);
    function setApprovedOracle(address oracle, bool approved) external;
    function setFeeRecipient(address recipient) external;
    function setMarketCreationFeeBps(uint16 bps) external;
    function setDefaultRedemptionFeeBps(uint16 bps) external;
    function setPerMarketRedemptionFeeBps(uint256 marketId, uint16 bps) external;
    function setDefaultPerMarketCap(uint256 cap) external;
    function setPerMarketCap(uint256 marketId, uint256 cap) external;

    // User (PUBLIC, pause-gated for MARKET module except refund/resolve/cancel)
    function splitPosition(uint256 marketId, uint256 amount) external;
    function mergePositions(uint256 marketId, uint256 amount) external;
    function resolveMarket(uint256 marketId) external;
    function redeemMarketTokens(uint256 marketId) external;
    function redeem(uint256 marketId, uint256 amount) external;
    function refund(uint256 marketId, uint256 amount) external;

    // Operator (OPERATOR_ROLE)
    function emergencyResolve(uint256 marketId, bool outcome) external; // after 7d delay
    function enableRefundMode(uint256 marketId) external;

    // Admin (ADMIN_ROLE)
    function sweepUnclaimed(uint256 marketId) external; // after 365d grace

    // View
    function getMarket(uint256 marketId) external view returns (MarketData memory);
    function getPerMarketCap(uint256 marketId) external view returns (uint256);
    function getEffectiveRedemptionFeeBps(uint256 marketId) external view returns (uint16);
    function isResolved(uint256 marketId) external view returns (bool);
    function getSafetyCaps() external view returns (uint256 perTradeCap, uint256 perMarketCap);
}
```

**Constants:**
```solidity
uint256 constant EMERGENCY_DELAY     = 7 days;
uint256 constant GRACE_PERIOD        = 365 days;
uint256 constant MAX_REDEMPTION_FEE_BPS = 1500; // 15%
uint256 constant BPS_DENOMINATOR     = 10000;
```

### 4.4 EventFacet

```solidity
interface IEventFacet {
    function createEvent(
        string calldata name,
        string[] calldata childQuestions,
        uint256 endTime,
        address oracle
    ) external returns (uint256 eventId, uint256[] memory marketIds);

    function resolveEvent(uint256 eventId) external;
    function enableEventRefundMode(uint256 eventId) external;

    function getEvent(uint256 eventId) external view returns (EventGroup memory);
    function getEventMembers(uint256 eventId) external view returns (uint256[] memory);
    function getMarketEventId(uint256 marketId) external view returns (uint256);
}
```

**EventGroup invariant**: Exactly 1 child market has `outcome=true` after `resolveEvent`; all others `outcome=false`. Mutual exclusion enforced on-chain.

### 4.5 DiamondCutFacet

Standard EIP-2535 with authority restriction:

```solidity
function diamondCut(
    FacetCut[] calldata _diamondCut,
    address _init,
    bytes calldata _calldata
) external;
```

**Access**: `onlyRole(CUT_EXECUTOR_ROLE)` — only TimelockController holds this role. Not even `DEFAULT_ADMIN_ROLE` can bypass (NEW-01 remediation).

### 4.6 AccessControlFacet

Standard OpenZeppelin-style, but with `renounceRole` + `_revokeRole` guard preventing last `DEFAULT_ADMIN_ROLE` removal.

```solidity
function grantRole(bytes32 role, address account) external;
function revokeRole(bytes32 role, address account) external;
function renounceRole(bytes32 role, address account) external;
function hasRole(bytes32 role, address account) external view returns (bool);
function getRoleMember(bytes32 role, uint256 index) external view returns (address);
function getRoleMemberCount(bytes32 role) external view returns (uint256);
function getRoleAdmin(bytes32 role) external view returns (bytes32);
function setRoleAdmin(bytes32 role, bytes32 adminRole) external;
```

### 4.7 PausableFacet

Module-keyed pause (not global):

```solidity
function pause(bytes32 module) external onlyRole(PAUSER_ROLE);
function unpause(bytes32 module) external onlyRole(PAUSER_ROLE);
function paused(bytes32 module) external view returns (bool);
```

**Pause semantics matrix** (see [Access Control](../smart-contracts/33-access-control-safety.md)):

| Function | Module gate |
|---|---|
| `createMarket`, `splitPosition`, `mergePositions`, `redeem*`, `placeOrder`, `buyYes/...` | MARKET module |
| `refund`, `resolveMarket`, `cancelOrder`, `emergencyResolve`, `enableRefundMode`, `sweepUnclaimed`, `fillAsChain` | NOT gated (exit paths always open) |

## 5. Package: `hook`

### 5.1 `PrediXHookV2` (Uniswap v4 IHooks implementation)

**State (in proxy storage):**
```solidity
address public diamond;         // immutable-like via initialize
address public usdc;
address public admin;
bool public paused;
mapping(PoolId => uint256) public poolToMarketId;
mapping(PoolId => address) public poolToYesToken;
mapping(address => bool) public trustedRouters;
```

**Hook permissions (bit-packed address):**
```solidity
function getHookPermissions() external pure returns (Hooks.Permissions memory) {
    return Hooks.Permissions({
        beforeInitialize: true,
        afterInitialize: true,
        beforeAddLiquidity: false,
        afterAddLiquidity: false,
        beforeRemoveLiquidity: false,
        afterRemoveLiquidity: false,
        beforeSwap: true,
        afterSwap: true,
        beforeDonate: false,
        afterDonate: false,
        beforeSwapReturnDelta: false,
        afterSwapReturnDelta: false,
        afterAddLiquidityReturnDelta: false,
        afterRemoveLiquidityReturnDelta: false
    });
}
```

Address must have salt-mined flag bits matching this permission set.

**Identity commit (anti-sandwich):**

```solidity
bytes32 private constant _COMMIT_SLOT = keccak256("predix.hook.identity_commit");

function commitIdentity(address caller) external {
    if (!trustedRouters[msg.sender]) revert Hook_UntrustedRouter(msg.sender);
    assembly { tstore(_COMMIT_SLOT, caller) }
}

function beforeSwap(address sender, PoolKey calldata key, ...) external returns (...) {
    if (msg.sender != address(poolManager)) revert Hook_NotPoolManager();

    address committed;
    assembly { committed := tload(_COMMIT_SLOT) }
    if (committed == address(0)) revert Hook_IdentityCommitMissing();

    // dynamic fee based on market.endTime - block.timestamp
    uint24 fee = _computeDynamicFee(marketId);
    return (IHooks.beforeSwap.selector, BalanceDelta.wrap(0), fee | LPFeeLibrary.OVERRIDE_FEE_FLAG);
}
```

**Dynamic fee:**
```solidity
function _computeDynamicFee(uint256 marketId) internal view returns (uint24) {
    uint256 timeLeft = endTime - block.timestamp;
    if (timeLeft > 7 days)    return 5000;   // 0.5%
    if (timeLeft > 3 days)    return 10000;  // 1.0%
    if (timeLeft > 1 days)    return 20000;  // 2.0%
    return 50000;                             // 5.0%
}
```

### 5.2 `PrediXHookProxyV2` (ERC1967 + 48h timelock)

**Upgrade state:**
```solidity
address public pendingUpgradeImpl;
uint256 public pendingUpgradeProposedAt;
bytes32 public pendingUpgradeSalt;
uint256 public timelockDuration = 48 hours;
address public admin;
```

**Functions:**
```solidity
function proposeUpgrade(address newImpl, bytes32 salt) external onlyAdmin;
function executeUpgrade() external onlyAdmin;  // requires elapsed >= timelockDuration
function cancelUpgrade() external onlyAdmin;
function setTimelockDuration(uint256 newDuration) external onlyAdmin;
function proposeAdminChange(address newAdmin) external onlyAdmin;
function acceptAdmin() external;
```

**`_authorizeUpgrade`**: Internal check:
```solidity
function _authorizeUpgrade(address newImpl) internal view override {
    if (newImpl != pendingUpgradeImpl) revert Hook_UpgradeMismatch();
    if (block.timestamp < pendingUpgradeProposedAt + timelockDuration)
        revert Hook_UpgradeTimelockNotMet(pendingUpgradeProposedAt + timelockDuration);
}
```

## 6. Package: `exchange`

### 6.1 `PrediXExchange` state

```solidity
struct Order {
    uint256 marketId;
    address owner;
    Side side;           // BUY_YES, SELL_YES, BUY_NO, SELL_NO
    uint256 price;       // 10000-990000 (6 decimals)
    uint256 amount;
    uint256 filled;
    uint256 depositLocked;
    uint64 placedAt;
    OrderStatus status;  // OPEN, FILLED, CANCELLED
}

// Storage (packed ~5 slots per order)
mapping(bytes32 => Order) private _orders;
mapping(uint256 => mapping(address => bytes32[])) private _userOrders; // max 50 per user per market

// Price bitmap per market (99 ticks)
mapping(uint256 => BitMaps.BitMap) private _bidBitmap;
mapping(uint256 => BitMaps.BitMap) private _askBitmap;
mapping(uint256 => mapping(uint256 => bytes32[])) private _orderQueueAtPrice; // FIFO
```

### 6.2 Enums

```solidity
enum Side { BUY_YES, SELL_YES, BUY_NO, SELL_NO }       // 0, 1, 2, 3
enum MatchType { COMPLEMENTARY, MINT, MERGE }           // 0, 1, 2
enum OrderStatus { OPEN, FILLED, CANCELLED }            // 0, 1, 2
```

### 6.3 Constants

```solidity
uint256 constant PRICE_MIN        = 10000;      // $0.01
uint256 constant PRICE_MAX        = 990000;     // $0.99
uint256 constant PRICE_STEP       = 10000;      // $0.01
uint256 constant MIN_ORDER_SIZE   = 1000000;    // $1
uint256 constant MAX_ORDERS_PER_USER = 50;
uint256 constant MAX_FILLS        = 20;
```

### 6.4 Matching algorithm

Pseudo-code cho `placeOrder(marketId, Side side, price, amount)`:

```
remaining = amount
fills = 0

while remaining > 0 && fills < MAX_FILLS:
  // Try COMPLEMENTARY first
  counterPartyPrice = findBest(opposite side)
  if match found && priceAcceptable:
    fill amount at best price (price improvement rule)
    update both orders
    emit OrderMatched(maker, taker, COMPLEMENTARY)
    continue

  // Try MINT if BUY side, MERGE if SELL side
  oppositeBuyOrSell = findBest(same direction, opposite token)
  if sum(price) >= 1.00 (MINT) || sum(price) <= 1.00 (MERGE):
    Diamond.splitPosition or mergePositions
    emit OrderMatched(maker, taker, MINT|MERGE)
    surplus/deficit → feeRecipient
    continue

  break

if remaining > 0:
  insert as resting order in bitmap + queue
  emit OrderPlaced
```

**Price improvement rule**: When taker's limit is more aggressive than maker's, fill at maker's price (benefit taker). Applies across all 3 MatchTypes.

### 6.5 Solvency invariant (INV-2)

```
Σ (order.depositLocked for all OPEN orders) ==
    USDC.balanceOf(exchange) + Σ (outcomeToken.balanceOf(exchange))
```

Fuzz-tested with 10K runs per CI run. Violation = critical bug.

## 7. Package: `router`

### 7.1 `PrediXRouter` — stateless design

**No storage variables** (except immutables):

```solidity
address public immutable diamond;
IPoolManager public immutable poolManager;
IPermit2 public immutable permit2;
IERC20 public immutable usdc;
```

All state flows via calldata/memory. Upgrade = redeploy (no storage migration).

### 7.2 Trading functions

```solidity
function buyYes(
    uint256 marketId,
    uint256 usdcIn,
    uint256 minYesOut,
    address recipient,
    uint256 deadline
) external returns (uint256 yesOut);

function sellYes(...) external returns (uint256 usdcOut);
function buyNo(uint256 marketId, uint256 usdcIn, uint256 mintAmount, uint256 minNoOut, address recipient, uint256 deadline) external returns (uint256 noOut);
function sellNo(...) external returns (uint256 usdcOut);

// Permit2 variants
function buyYesWithPermit2(..., PermitTransferFrom calldata permit, bytes calldata signature) external returns (uint256);
// (similar for sellYes / buyNo / sellNo)
```

### 7.3 Quote functions (revert-and-decode)

```solidity
function quoteBuyYes(uint256 marketId, uint256 usdcIn) external returns (uint256 yesOut) {
    // simulate full swap logic
    yesOut = ...;
    revert(abi.encode(yesOut)); // always revert — never returns normally
}
```

Caller uses try/catch to extract result from revert data.

### 7.4 Zero-custody invariant (INV-3)

```solidity
modifier assertZeroBalance() {
    _;
    if (usdc.balanceOf(address(this)) != 0) revert Router_NonZeroBalance();
    // Similarly for YES/NO tokens per market
}
```

Every public entry point decorated. Fuzz-tested 10K runs.

### 7.5 Identity commit to Hook

Before unlocking PoolManager:

```solidity
function buyYes(...) external assertZeroBalance {
    _pullTokens(usdcIn);

    // Commit caller to hook (EIP-1153)
    IHookCommit(address(hook)).commitIdentity(msg.sender);

    // Unlock + swap
    poolManager.unlock(abi.encode(swapParams));

    // _refundAndAssertZero runs via modifier
}
```

## 8. Invariant summary (test coverage)

| # | Invariant | Package | Test |
|---|---|---|---|
| INV-1 | `YES.totalSupply == NO.totalSupply == market.totalCollateral` (unresolved) | diamond | `CollateralSolvency.t.sol` |
| INV-2 | `Σ order.depositLocked == exchange's held tokens` | exchange | `ExchangeSolvency.t.sol` |
| INV-3 | `balanceOf(router) == 0` post-call | router | `RouterZeroCustody.t.sol` |
| INV-4 | `effectiveRedemptionFeeBps <= 1500` | diamond | `RedemptionFeeBound.t.sol` |
| INV-5 | `beforeSwap` requires identity commit | hook | `IdentityCommit.t.sol`, fork test |
| INV-6 | Resolution monotonic (isResolved=true immutable) | diamond | `ResolutionMonotonic.t.sol` |
| INV-7 | Only Diamond can mint/burn OutcomeToken | shared | `OutcomeTokenSupply.t.sol` |

Each invariant runs 10K fuzz iterations per CI. Details: [Invariants](../security/02-invariants.md).

## 9. Known design trade-offs (for auditor discussion)

### 9.1 ManualOracle = single-signer admin

**Trade-off**: Admin centralization vs. subjective event coverage.

**Mitigation**:
- Mainnet admin MUST be multisig (3/5 Phase 1 → 4/7 Phase 2)
- 48h challenge window before lock (Phase 2)
- Oracle approval revocable → `enableRefundMode` escape hatch
- Re-verify at resolve (FINAL-D-03)

### 9.2 Hook shared contract across markets

**Trade-off**: Single hook for all markets vs. per-market hook deploy.

**Decision**: Single hook. Lower gas, simpler ops. Per-pool state via `PoolId → marketId` mapping.

**Invariant**: Hook logic is market-agnostic except for `_computeDynamicFee(marketId)` which reads `MarketData.endTime` from Diamond.

### 9.3 Virtual NO pricing via YES pool only

**Trade-off**: Single YES/USDC pool vs. dual YES + NO pools.

**Decision**: Single. Simpler liquidity concentration. NO trades = split + swap YES + keep NO (or reverse for sell).

**Risk**: Extra gas for NO trades (~2x YES). Documented in [Virtual NO Pricing](../giao-dich/26-virtual-no-pricing.md).

### 9.4 Router zero-custody with `_refundAndAssertZero`

**Trade-off**: Assertion at end of every call vs. trusting internal logic.

**Decision**: Hard assertion for defense-in-depth. Small gas overhead.

## 10. Non-audit-scope components

The following are OUT of audit scope, but auditor should review for completeness:

- **Deployment scripts** (`SC/script/*.s.sol`): Verify TimelockController setup, role grants/revokes, Hook salt mining
- **Test helpers** (`test/mocks/`): Any mock that deviates from standard behavior
- **Frontend** (`FE_new/`): Trust model with backend; input sanitization
- **Backend** (`BE/`): SIWE verification correctness; admin endpoint gating

## 11. Future contract work (post-audit)

Not in scope for current audit, but planned:

| Version | Changes | Timeline |
|---|---|---|
| v2.1 | UMA Oracle adapter + dispute mechanism | Q2-Q3 2026 |
| v2.2 | Scalar market facet (continuous outcomes) | Q3 2026 |
| v2.3 | Cross-chain message receiver | Q4 2026 |
| v3.0 | Governance token + on-chain voting | 2027 |

Each new major version requires re-audit.

## 12. Appendices

### A. Custom errors registry

Full list: [Custom Errors](../smart-contracts/custom-errors.md). Summary:

- Diamond / MarketFacet: 15 errors (e.g., `Market_NotFound`, `Market_OracleNotApproved`)
- Exchange: 6 errors (`Exchange_InvalidPrice`, `Exchange_MaxOrdersExceeded`, etc.)
- Router: 4 errors (`Router_SlippageExceeded`, `Router_NonZeroBalance`, etc.)
- Hook: 6 errors (`Hook_IdentityCommitMissing`, `Hook_UntrustedRouter`, etc.)
- Oracle: 7 errors (`Oracle_StalePrice`, `Oracle_SequencerDown`, etc.)
- AccessControl: 2 errors (`AC_Unauthorized`, `AC_LastAdminProtection`)
- DiamondCut: 3 errors (`DC_SelectorAlreadyExists`, etc.)

### B. External dependencies (vendored in `lib/`)

- OpenZeppelin Contracts (ERC20, SafeERC20, AccessControl, ERC1967, TimelockController)
- Uniswap v4-core (PoolManager, Hooks, BalanceDelta, LPFeeLibrary)
- Uniswap v4-periphery (TickMath, FullMath, StateLibrary)
- Permit2 canonical at `0x000000000022D473030F116dDEE9F6B43aC78BA3`
- Chainlink `AggregatorV3Interface`

### C. Compilation settings

```toml
# foundry.toml (per package)
solc = "0.8.30"
evm_version = "cancun"
via_ir = true
optimizer = true
optimizer_runs = 200
bytecode_hash = "none"
```

### D. Test suite statistics (2026-04-17)

| Package | Tests | LOC |
|---|---|---|
| shared | 51 | ~2K |
| oracle | 76 | ~3K |
| diamond | 293 | ~18K |
| hook | 141 | ~9K |
| exchange | 99 | ~7K |
| router | 83 | ~6K |
| **Total** | **743** | **~45K** |

Fuzz runs: 10K per invariant, 256 per property test. CI runtime: ~8 minutes on 4 vCPU.

### E. Reference docs

- [Contract Architecture](../smart-contracts/27-contract-architecture.md)
- [Invariants](../security/02-invariants.md)
- [Audit Reports](../security/01-audits.md)
- [Oracle Design](02-oracle-design.md)
- [Security Design](04-security-design.md)
