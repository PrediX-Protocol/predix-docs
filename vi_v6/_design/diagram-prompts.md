# PrediX Diagram Design Prompts

File này dùng để feed cho AI design tool (Claude Artifacts, v0.dev, Figma AI, hoặc designer) tạo hình SVG/PNG thay thế Mermaid diagrams trong docs.

> **Lưu ý**: AI image gen (Midjourney, DALL-E) không tốt cho diagram có text — hay hallucinate chữ. Recommend dùng tool tạo SVG: Claude Artifacts, v0.dev, Excalidraw, Whimsical, hoặc Figma.

---

## 1. Design system — dùng cho MỌI diagram

```
You are designing diagrams for PrediX Protocol documentation (prediction market on Unichain blockchain). All diagrams must follow this design system:

Brand colors:
- Primary blue: #2563eb (accent, CTA, highlights)
- Dark background: #0f172a (if dark mode) or #ffffff (light mode)
- Surface: #1e293b (card/node background dark) or #f8fafc (light)
- Border: #334155 (dark) or #e2e8f0 (light)
- Text primary: #f1f5f9 (dark) or #0f172a (light)
- Text secondary: #94a3b8
- Success/positive: #22c55e
- Error/negative: #ef4444
- Warning: #f59e0b
- Burn/fire: #dc2626

Typography:
- Font: Inter or system sans-serif
- Node labels: 14px semibold
- Sublabels: 12px regular, secondary color
- Title: 18px bold

Style rules:
- Rounded corners (8px radius) on all nodes
- 1.5px borders, no drop shadows
- Arrows: 2px, color #64748b, rounded connectors
- Consistent 24px spacing between nodes
- Max width 800px for horizontal, max height 600px for vertical
- Clean, minimal — no gradients, no 3D, no decorative elements
- Professional DeFi docs aesthetic (reference: Aave, Uniswap, Ethena docs style)
- Output as SVG (preferred) or PNG @2x (1600px wide)
- Dark mode version primary, light mode version optional
```

---

## 2. Pie chart — Token allocation

Dùng cho: `kinh-te/README.md` (PRX Allocation), `khai-niem/phi.md` (Fee distribution)

```
Create a clean pie/donut chart for PrediX token allocation.

[DESIGN SYSTEM above]

Chart type: Donut chart (hollow center)
Center text: "1B PRX" (bold, 24px)

Segments (clockwise from top):
- Community & Ecosystem: 30% — color #2563eb
- Investors: 20% — color #7c3aed  
- Team & Advisors: 18% — color #06b6d4
- Treasury / DAO: 17% — color #f59e0b
- Marketing & Partners: 10% — color #ec4899
- Liquidity DEX/CEX: 5% — color #22c55e

Legend: right side, vertical list with color dot + label + percentage
No 3D, no exploding slices
Clean white labels on segments (percentage only, e.g. "30%")
```

---

## 3. Flowchart — User process flow (vertical)

Dùng cho: trade flow, bridge flow, claim flow, staking flow, SIWE auth flow, etc.
Đây là loại phổ biến nhất (~40 diagrams).

```
Create a vertical process flow diagram for PrediX.

[DESIGN SYSTEM above]

Flow direction: top to bottom
Node types:
- Start node: rounded pill shape, primary blue (#2563eb), white text, icon optional
- Process step: rounded rectangle, surface color (#1e293b), white text
- Decision diamond: surface color with blue border
- End node: rounded pill, green (#22c55e), white text

Content — [SPECIFIC FLOW HERE]:

Example — Trade flow:
1. [START] User chọn market + side (YES/NO)
2. [STEP] Nhập amount USDC + review preview
3. [STEP] Router quote: CLOB check → AMM fallback
4. [DECISION] Slippage OK?
   - Yes → [STEP] Execute swap (1 tx)
   - No → [END-RED] Tx revert, tiền không mất
5. [STEP] Nhận outcome tokens về ví
6. [END-GREEN] Trade complete

Arrow labels: small text (12px), muted color
Max 6-8 steps per diagram
```

**Danh sách flow cần tạo** (thay [SPECIFIC FLOW] ở trên):

| File | Flow name | Steps tóm tắt |
|---|---|---|
| `bat-dau/giao-dich-dau-tien.md` | First trade | Connect → Bridge → Pick market → Buy YES → Done |
| `bat-dau/bridge.md` | Bridge to Unichain | Chọn source → Connect → Amount → Confirm → Wait → Done |
| `bat-dau/ket-noi-vi.md` | Wallet connect | Chọn method → Passkey/MetaMask → Sign → Connected |
| `bat-dau/ket-noi-vi.md` | SIWE auth | Challenge → Sign → Verify → Session |
| `huong-dan/giao-dich-market.md` | Router waterfall | Quote → CLOB try → AMM fallback → Cleanup → Done |
| `huong-dan/redeem-va-claim.md` | Claim all (batch) | Click Claim All → Bundle calls → Execute → Burn tokens → USDC về ví |
| `huong-dan/redeem-va-claim.md` | Admin refund | Admin enable → User burn YES+NO → Receive USDC |
| `huong-dan/tao-market.md` | Create market | Fill form → Oracle select → Params → Deploy → Pool init → Live |
| `huong-dan/cung-cap-thanh-khoan.md` | LP after resolve | Market resolve → Pool freeze → Remove LP → Redeem tokens → Done |
| `kinh-te/staking-real-yield.md` | Staking flow | Deposit PRX → Mint stkPRX → Weekly yield → Claim USDC |
| `kinh-te/buyback-burn.md` | Buyback flow | Collect fees → Swap USDC→PRX → Burn → Supply giảm |
| `kinh-te/rewards.md` | Reward box VRF | Trade → Drop chance → Box appear → Open → VRF random → Reward |
| `kinh-te/veprx-gauge.md` | Bribe market | Project deposit bribe → Voters vote gauge → Epoch end → Subsidy + bribe split |
| `developers/router-integration.md` | AA UserOp batch | Encode calls → Build UserOp → Paymaster sign → Bundler submit → Execute |
| `developers/api-reference.md` | SIWE auth | Same as ket-noi-vi SIWE |

---

## 4. Flowchart — Architecture / dependency (horizontal or vertical)

Dùng cho: smart contract dependency, protocol layers, data flow

```
Create an architecture diagram for PrediX.

[DESIGN SYSTEM above]

Layout: top-to-bottom layered
Node style: rounded rectangle with bold title + italic subtitle (2 lines)
Arrows: dependency direction (top depends on bottom)

Content — Protocol layers:

Layer 1 (top): User → HTTPS → FE (Next.js + React)
Layer 2: FE → REST → BE (NestJS + MongoDB)  
Layer 3: BE → REST → Indexer (Ponder + PostgreSQL)
Layer 4 (bottom): Indexer → RPC → Smart Contracts (Solidity, Unichain)
Side arrow: User → tx direct → Smart Contracts (dotted)

Color coding:
- Smart Contracts: darkest (ground layer)
- Indexer/BE/FE: progressively lighter
- User: accent blue
```

**Danh sách architecture cần tạo**:

| File | Diagram | Content |
|---|---|---|
| `giao-thuc/README.md` | 4-layer stack | FE → BE → Indexer → SC |
| `giao-thuc/architecture.md` | SC dependency graph | shared → oracle/diamond → hook/exchange → router |
| `giao-thuc/bao-mat.md` | 5-layer defense | Timelock → Access control → Invariants → Audit → Bug bounty |
| `giao-thuc/oracle.md` | Oracle types | Pluggable: Manual / Chainlink / UMA → Diamond |
| `khai-niem/clob-va-amm.md` | Hybrid routing | Order → CLOB try → AMM fallback → Best price |

---

## 5. State diagram

Dùng cho: market lifecycle, hook upgrade, unstake cooldown

```
Create a state machine diagram for PrediX.

[DESIGN SYSTEM above]

States: rounded rectangles with state name (bold) + optional description (small text)
Transitions: arrows with label text
Initial state: small filled circle
Final state: bullseye circle

Content — Market lifecycle:

States: Active → Ended → Resolving → Resolved → Redeemable
Alternative: Active → Ended → RefundMode (if emergency)
Transitions labeled with trigger (endTime reached, oracle report, admin action)
```

**Danh sách state cần tạo**:

| File | Diagram | States |
|---|---|---|
| `khai-niem/resolution.md` | Market lifecycle | Active → Ended → Resolving → Resolved / RefundMode |
| `giao-thuc/architecture.md` | Hook upgrade | Idle → Proposed → (48h wait) → Executed / Cancelled |
| `kinh-te/staking-real-yield.md` | Unstake cooldown | Staked → CooldownRequested → Cooldown (7d) → Withdrawable |

---

## 6. Gantt chart — Vesting timeline

```
Create a horizontal timeline/gantt chart for PRX token vesting.

[DESIGN SYSTEM above]

X-axis: months from TGE (0 to 54)
Y-axis: token buckets (Community, Investors, Team, Treasury, Marketing, Liquidity)

Each bucket = horizontal bar:
- Solid fill for cliff period (no unlock)
- Gradient/striped fill for linear vesting period
- Marker at start for TGE unlock %

Color per bucket (match pie chart colors)
Clean grid lines, month labels at 6mo intervals
```

---

## 7. Mindmap

Dùng cho: concept overview, feature tree

```
Create a radial mindmap for PrediX.

[DESIGN SYSTEM above]

Center: main concept (large, accent blue)
Level 1 branches: 4-6 main categories (medium, surface color)
Level 2 leaves: 2-4 items per category (small, muted)

Branches: organic curved lines, not straight
Color: each L1 branch slightly different shade
No icons unless specified

Content — [SPECIFIC MINDMAP]:

Example — PRX Token:
Center: PRX Token
- Allocation: Community 30%, Investors 20%, Team 18%, Treasury 17%, Marketing 10%, Liquidity 5%
- Utility: Stake yield, vePRX vote, Fee discount, Market creation bond
- Demand sinks: Adaptive buyback, veToken lock, Gauge voting
- Governance: Protocol params, Treasury spend, Phase transition
```

**Danh sách mindmap cần tạo**:

| File | Topic | Branches |
|---|---|---|
| `kinh-te/README.md` | PRX Token | Allocation, Utility, Demand, Governance |
| `khai-niem/README.md` | Core concepts | Market, Token, Trading, Resolution, Fee |
| `kinh-te/rewards.md` | Badges | Volume, Accuracy, Streak, Special |
| `kinh-te/veprx-gauge.md` | vePRX rights | Gauge, Params, Treasury, Phase, Emergency |
| `tai-nguyen/settings-i18n.md` | Settings | Profile, Trading, Notifications, Privacy, Developer |

---

## 8. Comparison table as visual (optional)

Nếu muốn biến table thành visual:

```
Create a feature comparison card for PrediX trading mechanism.

[DESIGN SYSTEM above]

3-column layout:
- Column 1: "CLOB only" — icon: order book — features list
- Column 2: "AMM only" — icon: pool — features list  
- Column 3 (highlighted): "Hybrid (PrediX)" — icon: both — features list with checkmarks

Highlight column 3 with accent blue border
Each feature row: icon + text, green check or red X
Clean card style, no heavy borders
```

---

## Workflow gợi ý

1. Copy design system prompt (§1) + diagram-specific prompt (§2-8) → paste vào AI tool
2. Thay [SPECIFIC FLOW/CONTENT] bằng data thực từ docs
3. AI generate SVG → save vào `vi_v6/_design/assets/`
4. Replace `mermaid` code block trong .md bằng `![alt](../_design/assets/filename.svg)`
5. Test trên GitBook preview

Recommend tools:
- **Claude Artifacts** (tốt nhất cho SVG có text chính xác)
- **v0.dev** (React component → export SVG)
- **Excalidraw** (hand-drawn style, clean)
- **Figma** (nếu cần polish nhất)
- **Whimsical** (flowchart chuyên dụng)

Avoid: Midjourney / DALL-E (hallucinate text trong diagram).
