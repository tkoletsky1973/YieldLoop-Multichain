YIELDLOOP
AI System & Rules Engine
Architecture, Strategy Eligibility, Stop-Loss Execution, Vault Accelerator Scoring, Authority Matrix & Failure Modes | v5.2
| v5.2 changes: AUM strategy gates removed — strategy eligibility is vault-driven. Stop-loss is autonomous. Quarterly ratchet minimum added. Arb pool mechanics fully defined. Multi-vault scoring clarified. |
| --- |


# 1. System Architecture
The YieldLoop AI system has three components working in sequence. The Optimization Model produces recommendations. The Safety Layer validates and enforces all invariables. The Communication Layer translates everything into plain language for users. No component can bypass the Safety Layer.
## 1.1 Component Overview
| Component | Technology |
| --- | --- |
| Optimization Model | XGBoost-class gradient boosting |
| Safety Layer | Deterministic rules engine |
| Communication Layer | Gemini Flash 2.0 (primary) |


## 1.2 Decision Flow
| USER ACTION / EPOCH CLOSE / MARKET EVENT ↓ Optimization Model (produces candidate recommendations) ↓ Safety Layer (validates each recommendation against invariables) PASS → forward to execution FAIL → veto, log anomaly, use last-valid parameters ↓ Execution Layer (smart contracts execute — vault rebalance, trade, arb entry/exit) ↓ Communication Layer (generates user notification / dashboard update / epoch summary) |
| --- |


# 2. Optimization Model
## 2.1 Strategy Eligibility — Vault-Driven
There are no AUM-based strategy gates. The model evaluates each vault individually and determines what strategies are appropriate based on vault size, risk tier, current market conditions, gas economics, and pool health. A $1,000 vault can access spot trading, staking, lending, LP, and arb from day one if the model determines those strategies fit the vault.

| Strategy | Model Considers |
| --- | --- |
| Venus Lending | Venus supply APR vs alternatives |
| BNB Staking | stkBNB/ankrBNB APR, unbonding liquidity |
| Stable LP | Pool APR, gas cost, IL risk |
| Spot Trading | Token volatility, spread, position sizing |
| XRP/SOL Spot | Wrapped token liquidity, PCS vs BiSwap spread |
| Mixed LP (BTC/ETH/BNB) | Pool depth, IL risk, vault size |
| Cross-DEX Arb | Pool depth, spread opportunity, gas |

These are model guidelines, not contract locks. The Safety Layer does not enforce strategy minimums — only invariables. The model applies judgment. A $900 vault asking for Growth tier spot trading will receive a recommendation — it may just be heavily weighted toward stable strategies for its own protection.
## 2.2 Model Inputs
### Vault-Level Inputs
- Balance in USDT and current allocation per token and strategy
- Risk tier: Conservative, Balanced, or Growth
- Vault age in epochs and deposit history for trailing 6 months
- Current accelerator score and earned reduction
- NFT tier and associated cap levels
- Gas reserve level and consumption history
- Overflow routing destination and proximity to ceiling
- Multi-vault context: other vaults on same wallet, combined balance

### Protocol-Level Inputs
- Total AUM across all chains — for ratchet trigger assessment only, not strategy gating
- Current protocol base rate and vault's effective rate
- Total LOOP outstanding and current redemption value
- Redemption pool and ratchet pool health vs floors
- Arb pool total size, recent performance, and current positions
- System vault balance and overflow queue

### Market Inputs
- Chainlink prices for all 8 tokens — current and TWAP
- 30-day rolling volatility per token
- PCS V3 and BiSwap prices for all active pairs — for best execution routing
- Venus supply rates for USDT, USDC, DAI
- Beefy/Krystal LP APRs for active pools
- stkBNB and ankrBNB APR and unbonding queue depth
- Current BSC gas price and projected gas cost per strategy operation

## 2.3 Strategy Allocation Output
Per vault, per epoch:

| { allocations: { venus_usdt_pct, venus_usdc_pct, venus_dai_pct, staking_stkbnb_pct, staking_ankrbnb_pct, lp_stable_pairs: [{ pool, pct }], lp_mixed_pairs: [{ pool, pct }], spot_btc_pct, spot_eth_pct, spot_bnb_pct, spot_xrp_pct, spot_sol_pct, arb_pool_contribution_pct, gas_reserve_pct }, non_stable_total_pct, // Safety Layer checks <= 30% routing: { xrp: 'pcs' | 'biswap', // best execution sol: 'pcs' | 'biswap' }, take_profit_thresholds: { btc, eth, bnb, xrp, sol }, stop_loss_thresholds: { btc, eth, bnb, xrp, sol }, slippage_tolerances: { btc, eth, bnb, xrp, sol }, rebalance_frequency_days } |
| --- |

## 2.4 Stop-Loss Execution
Stop-loss is autonomous. When the Optimization Model detects that a spot position has reached its stop-loss threshold, it issues an immediate sell order. There is no user notification window, no override opportunity, and no delay. Speed is the entire value of a stop-loss — waiting defeats the purpose in fast-moving markets.

STOP-LOSS FLOW:

1. Price oracle (Chainlink) confirms threshold breach
   TWAP check: price must be below threshold for 2 consecutive
   price updates to prevent flash-crash false triggers

2. Safety Layer validates: position exists, vault is not paused

3. Execution Layer: routes to best DEX (PCS vs BiSwap),
   executes sell at market with pre-set slippage tolerance

4. Proceeds: converted to USDT, added to vault available balance

5. Communication Layer: immediate notification to user
   Notification includes:
     Token sold, quantity, execution price
     Threshold that triggered the stop
     USDT received
     Reason summary from AI (e.g. 'BTC fell 18% from entry')

6. AI re-evaluates vault allocation at next rebalance window

Simultaneous stop-loss events across many vaults (e.g. broad market crash): the Safety Layer enforces an AUM-level rate limit on liquidations — maximum 10% of protocol AUM in urgent exits per 24 hours. Stop-loss executions are processed in queue order. Users are notified of any queue delay. This prevents protocol-level slippage cascade.

## 2.5 Take-Profit Execution
TAKE-PROFIT FLOW:

1. Price oracle confirms threshold reached

2. Execution Layer: sells position at market, best DEX routing

3. Proceeds: split per vault's profit distribution setting
   (USDT to claimable balance / LOOP to epoch accumulator)

4. AI sets new entry target — may re-enter same token at lower price

5. User notified: token, quantity, price, USDT/LOOP split

## 2.6 Vault Accelerator Scoring
The model produces an accelerator score for each vault at each epoch close. The score translates to a recommended rate reduction (0 to 2pp). The Safety Layer enforces that the new rate cannot exceed the current rate — reductions only.

SCORING SIGNALS (~weights):

  Balance:            ~30%  Vault USDT value — larger = higher score

  Deposit Volume:     ~25%  Total USDT in over trailing 6 months

  Deposit Frequency:  ~25%  Count of deposits in trailing 6 months
                           Consistent monthly beats quarterly lump sum

  Deposit Quality:    ~20%  Composite:
    Consistency score:      Regular interval deposits
    Net positive trend:     More in than out over 6 months
    Low withdrawal freq:    Fewer partial exits

OUTPUT PER VAULT:

  current_vault_rate     (Safety Layer will enforce new <= current)
  recommended_vault_rate (AI's recommended new rate)
  accelerator_reduction  (0.0 to 0.02)
  signal_breakdown       (all four scores for dashboard display)
  is_dormant             (bool — no activity in 30 days)
  human_summary          (plain language for dashboard)
  multi_vault_note       (if wallet has other vaults — shown in context)

DORMANCY:

  is_dormant = true when no qualifying activity in trailing 30 days
  Effect: accelerator_reduction frozen at current value
  Protocol-wide base rate drops STILL APPLY during dormancy
  Resume: one qualifying deposit resumes scoring from frozen value

## 2.7 LOOP Ratchet Recommendation
Model recommends ratchet increment when:

  redemption_pool >= 105% of LOOP liability  AND
  ratchet_pool >= 60-day projected demand     AND
  hours_since_last_ratchet >= 72

Recommended increment range: 0.25%–2.0%

Model considers: pool health margin, protocol growth rate,
  LOOP outstanding, projected redemption demand

HARD QUARTERLY MINIMUM OVERRIDE:

  IF days_since_last_ratchet >= 90
  AND redemption_pool >= floor
  AND ratchet_pool >= floor:

    RATCHET FIRES AT NEXT EPOCH CLOSE
    Minimum increment: 0.25%
    Safety Layer enforces — model cannot veto this
    This is a protocol commitment to LOOP holders

## 2.8 Arb Pool Management
CONTRIBUTION ALLOCATION:

  Model determines per-vault arb_pool_contribution_pct
  Considers: vault size, risk tier, pool depth, recent arb performance

  Conservative tier: 0–10% (only if vault >= $3K and pool healthy)
  Balanced tier: 10–20%
  Growth tier: 15–30%

PROFIT CALCULATION (epoch close):

  vault_share = vault_contribution / total_pool_value (at epoch open)
  vault_profit = total_epoch_arb_profit × vault_share

EXIT MECHANICS:

  Standard vault closure:
    Queued exit — share paid as positions close, max 48 hours
    No urgency premium on arb settlement

  Urgent vault exit:
    Arb share settles separately within 48 hours
    No urgency premium on arb portion
    Shown as separate pending settlement on dashboard

  Mid-epoch allocation reduction:
    Reduction applied at next epoch boundary
    No mid-epoch partial exits from arb pool

# 3. Safety Layer — Complete Rule Set
## 3.1 Fee Invariables
RULE: new_protocol_base_rate <= current_protocol_base_rate

  ALWAYS. No exception. No multisig override.
  Veto: any output suggesting rate increase → rejected, logged, alerted

RULE: new_vault_rate <= current_vault_rate

  Accelerator only reduces rates. Never increases.
  Veto: any upward accelerator recommendation → rejected, logged

## 3.2 Non-Stable Cap
RULE: non_stable_total <= 0.30 × vault_value [INVARIABLE]

  non_stable_total = BTC + ETH + BNB + XRP + SOL allocations

  If model output breaches cap:
    Redistribute excess to stablecoin strategies
    Log anomaly: 'Model exceeded non-stable cap'
    Alert admin dashboard
    User not impacted — silent rebalance

## 3.3 Pool Floor Enforcement
RULE: ratchet cannot fire if either pool is below floor

  redemption_pool < 105% of LOOP liability → veto ratchet
  ratchet_pool < 60-day demand → veto ratchet

  Exception: quarterly minimum still fires if BOTH pools are above floors

## 3.4 Stop-Loss Rate Limiting
RULE: urgent liquidations (stop-loss + emergency exits combined)
  <= 10% of total protocol AUM per 24-hour window [INVARIABLE]

  Queue overflow: execute in order, user notified of position in queue
  Queue maximum wait: 24 hours — escalate to multisig review if exceeded

## 3.5 DEX Routing Validation
RULE: XRP and SOL trades must use BestExecutionRouter output

  Cannot route to DEX with worse price than available alternative
  Slippage tolerance must be within bounds for wrapped BEP-20 tokens

  If both DEXes unavailable: spot/arb trading suspended for those tokens
  Stable strategies continue unaffected

## 3.6 Dormancy and Lite NFT Clock
RULE: Lite NFT clock runs continuously from vault application date

  Dormancy does NOT pause the Lite clock [INVARIABLE]
  Accelerator scoring IS paused during dormancy (separate rule)

  Lite validity check at each epoch: days_since_application < 395
  At expiry: Lite discount removed, vault rate recalculated

## 3.7 LOOP Forfeiture on Immediate Exit
RULE: fully issued past-epoch LOOP is NEVER forfeited

  LOOP with issuance date < current epoch start: always safe

  RULE: current incomplete epoch LOOP IS forfeited on immediate exit
  LOOP accumulated in current epoch (not yet issued): forfeited

  Forfeiture value in USD must be shown before exit confirmation
  This rule cannot be overridden by any party

# 4. Communication Layer
## 4.1 Vault Setup Conversation
The Communication Layer conducts the initial AI conversation during vault creation. It asks plain-language questions about the user's goals, translates answers into risk tier and strategy preferences, presents the Optimization Model's recommendation in plain language including worst-case scenarios in dollar terms, explains the fee trajectory, and produces the settings summary that the user acknowledges.

CONVERSATION STRUCTURE:

  1. Goal discovery: 'What are you trying to do with this money?'
  2. Risk assessment: plain-language questions, no jargon
  3. Strategy recommendation: tier + allocation with dollar examples
  4. Worst-case presentation: 'In a bad month, this could lose $X'
  5. Fee trajectory: current rate, how it drops, realistic timeline
  6. Token exposure: which tokens, why, what XRP/SOL BEP-20 means
  7. Settings confirmation: full summary before acknowledgment screen
  8. Offer to adjust: 'Want to change anything?'

  Advanced Mode: user can set individual strategy weights, specific
  token allocations, stop-loss thresholds, profit distribution ratio

## 4.2 Epoch Summary Notification
EPOCH SUMMARY — generated at each epoch close:

Dashboard: always displayed, no opt-out
Email: opt-in at signup and in settings

Content:

  Your vault this month:
    USDT settled and available to claim
    LOOP issued (quantity × rate at issuance)
    Fees paid (on profits only — shown in dollars)
    Net profit after fees

  Your rate:
    Current effective rate
    Accelerator change this epoch (if any)
    Signal breakdown: which signals improved/declined

  Protocol:
    Current AUM and progress to next fee milestone
    Protocol base rate (unchanged or dropped this epoch)
    Ratchet event (if fired: increment, new LOOP value)
    Days since last ratchet (quarterly minimum reminder if > 60 days)

  Credit pool:
    Credits distributed this epoch (Core/Maxi holders only)
    Cumulative credit balance

## 4.3 Stop-Loss Notification
Immediate push notification + email (regardless of email opt-in):

  'Stop-loss executed — BTC position'
  Token: BTC (BTCB)
  Quantity sold: 0.0142 BTC
  Execution price: $41,820
  Stop-loss threshold: $42,000
  USDT received: $593.84
  Reason: 'BTC fell 14.2% from your entry price of $48,750'
  Time: 2025-03-14 09:23:11 UTC
  Transaction: 0x7f3a...
  Your vault balance after: $1,847.20 USDT

  'The AI has noted this and will reassess your BTC
   allocation at the next rebalance window.'

## 4.4 Fee Drop Notification
Protocol-wide event — notified to ALL active vault holders:

  Dashboard: green banner (persistent until dismissed)
  Email: sent regardless of opt-in setting
  Push: immediate

  '🎉 Protocol fee dropped to 11%!'
  'Total protocol AUM crossed $1,000,000.'
  'Every vault fee has just permanently decreased.'
  'Your vault: was 12.0% → now 11.0%'
  'This drop is permanent — it can never be reversed.'
  'Next drop: 10% at $5M AUM — currently $1.2M [24% there]'

## 4.5 Referral Credit Notification
At T+30 days from new user's first deposit (if eligible):

  To referrer:
    '$5 deposit credit added to your account'
    '[Username/masked address] joined using your referral code'
    'Credit applies to your next deposit of $250 or more'

  To new user:
    '$5 deposit credit added to your account'
    'Welcome bonus: 30 days with YieldLoop'
    'Credit applies to your next deposit of $250 or more'

# 5. Authority Matrix
## 5.1 What the AI CAN Do
Score vault accelerator — recommend downward rate adjustments only

Allocate vault strategy across 8-token basket — within 30% non-stable cap

Select DEX routing (PCS vs BiSwap) per token per trade — best execution

Set take-profit and stop-loss thresholds per spot token

Execute stop-loss autonomously when threshold is reached

Manage arb pool contribution allocation per vault

Recommend LOOP ratchet increment — cannot veto quarterly minimum

Recommend pool buffer targets within invariable floors

Generate all user-facing notifications and summaries

## 5.2 What the AI CANNOT Do
Affect the protocol base rate — AUM ratchet contract only

Raise a vault's effective rate — Safety Layer vetoes any upward move

Exceed the 30% non-stable cap — Safety Layer redistributes excess

Pause the Lite NFT clock — runs continuously once applied

Veto the quarterly ratchet minimum — it fires regardless of model output

Add or remove tokens from the whitelist — governance only

Access, move, or freeze user funds directly — non-custodial architecture

Override any INVARIABLE parameter

## 5.3 What Nobody Can Do
Raise the protocol fee — structurally impossible, no mechanism exists

Reverse a crossed AUM ratchet threshold — permanent on-chain state

Lower the LOOP redemption value — ratchet is one-direction only

Bypass the 72-hour protocol pause maximum without 5-of-5 + public notice

Deploy mainnet without completed audit and active bug bounty

Change an INVARIABLE without 5-of-5 multisig + re-audit + 30-day notice

# 6. Multi-Vault Scoring
When a wallet operates multiple vaults, the AI scores each vault independently. The accelerator score for Vault 1 does not affect or inherit from Vault 2. However, the Communication Layer is aware of multi-vault context and presents a unified view.

MULTI-VAULT AI BEHAVIOR:

  Accelerator: scored per vault independently
  Strategy: recommended per vault independently
    (different risk tiers per vault are fully supported)
  Stop-loss: executed per vault independently
  Notifications: grouped by wallet — single epoch summary
    shows all vaults in one notification
  Fee trajectory: shown per vault and combined in dashboard
  Governance eligibility: communicated as combined wallet balance
    'Your combined vault balance: $14,200 — governance eligible'
  Referral code: one per wallet (not per vault)
  Tax export: filterable by vault or all vaults combined

# 7. Failure Modes
## 7.1 Optimization Model Failure
Invalid or unparseable output from model → Safety Layer rejects entire recommendation batch. All affected vaults hold last-valid parameters. Accelerator scores frozen. Admin notified immediately. Rollback via 3-of-5 multisig. User notification: 'Vault strategy update temporarily delayed — your current settings remain active.'
## 7.2 Communication Layer Failure
Gemini Flash 2.0 unavailable → automatic fallback to Claude Haiku. Claude Haiku unavailable → fallback to GPT-4o Mini. All three unavailable → vault creation paused (new vaults only). All running vaults continue operating on last-valid parameters. Users notified of delay. No impact to existing vault operations.
## 7.3 Stop-Loss Queue Overflow
If stop-loss queue exceeds 10% AUM limit: positions queue in order of threshold breach time. Users notified of queue position and estimated execution time. If queue wait exceeds 24 hours: escalate to multisig review. This scenario requires an extreme market event affecting many vaults simultaneously.
## 7.4 Oracle Failure
Chainlink feed staleness > 2× heartbeat interval → pause trading for affected token. TWAP used as fallback for up to 1 epoch. Admin notified. If TWAP also unavailable → token suspended from all activity until feed restored. Stablecoin strategies continue unaffected.
## 7.5 DEX Unavailability
PCS unavailable → route all trades to BiSwap only. BiSwap unavailable → route all trades to PCS only. Both unavailable → spot and arb trading suspended. Staking, lending, and LP strategies continue. User notified of suspension. Resumes automatically when DEX availability is restored.
## 7.6 Arb Pool Disruption
Arb pool depth falls below minimum viable threshold → AI stops new contributions to pool. Existing contributions held. Pool continues operating on remaining depth. New contributions resume when pool health is restored. Vault holders with arb allocations notified of temporary suspension.
## 7.7 Quarterly Ratchet Override
If the Safety Layer triggers the quarterly minimum ratchet but the Communication Layer is unavailable: the ratchet still fires. The contract executes. Notifications are queued and delivered when the Communication Layer recovers. The ratchet does not wait for the Communication Layer.

# 8. Audit Scope — v5.2 AI-Relevant Items
Additional audit scope items for v5.2:

• Stop-loss execution: verify autonomous execution cannot be manipulated
  to trigger at wrong price or be front-run by protocol participants

• Quarterly ratchet minimum: verify Safety Layer override of model veto
  fires correctly and cannot be bypassed

• LOOP forfeiture rule: verify past-epoch LOOP is provably non-forfeit
  and current epoch LOOP forfeiture calculation is correct

• Arb pool exit queue: verify 48-hour maximum is enforced
  and no urgency premium is applied to arb settlement

• Lite NFT clock: verify dormancy does not pause the clock
  and expiry is calculated correctly from application date

• Multi-vault governance: verify combined balance calculation
  cannot be gamed by vault splitting/merging

YieldLoop Protocol v5.2 — Confidential
