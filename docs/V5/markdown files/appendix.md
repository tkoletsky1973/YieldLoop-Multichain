YieldLoop — Appendixes  |  v5.2

# YIELDLOOP

## Appendixes

### Technical Appendixes A–L: Formulas, Parameters, Token Basket, Fee Ratchet, NFT Economics, Break-Even, Credit Pool, Arb Pool, Tax Export Fields & Glossary | v5.2

---

# Appendix A — Mathematical Formulas

## A1. LOOP Redemption Value

LOOP_Value(epoch_e, time_t) =

  1.00 × PRODUCT(1 + ratchet_i)

  for all ratchet events i fired after epoch e issuance through time t

Example — 4 ratchet events after issuance:

  +0.75%, +0.50%, +1.00%, +0.60%

  1.00 × 1.0075 × 1.005 × 1.010 × 1.006 = $1.0293 per LOOP

Floor: $1.00 per LOOP (issuance value — never drops below this)  
Ceiling: no cap — ratchet compounds indefinitely

---

## A2. Protocol Base Rate — AUM Ratchet

PROTOCOL_BASE_RATE = f(total_protocol_AUM_all_chains):

  AUM < $1M:    12%  [launch rate]  
  AUM >= $1M:   11%  [automatic, permanent]  
  AUM >= $5M:   10%  [automatic, permanent]  
  AUM >= $15M:   9%  [automatic, permanent]  
  AUM >= $40M:   8%  [automatic, permanent]  
  AUM >= $100M:  7%  [permanent floor]

Trigger: on-chain AUM check at each vault interaction + epoch close  
AUM aggregated across all chains (BSC hub + all spokes)  
No multisig, no delay — instant and permanent on threshold crossing  
Direction constraint: PROTOCOL_BASE_RATE[t+1] <= PROTOCOL_BASE_RATE[t] always

---

## A3. Vault Accelerator Rate

VAULT_RATE = PROTOCOL_BASE_RATE - ACCELERATOR_REDUCTION

ACCELERATOR_REDUCTION ∈ [0.0, 0.02]  (0 to 2pp)

Safety Layer invariable:

  new_VAULT_RATE <= current_VAULT_RATE at all times  
  (AI can only recommend downward adjustments)

Dormancy (no qualifying activity in 30 days):

  ACCELERATOR_REDUCTION frozen at current value  
  PROTOCOL_BASE_RATE drops still apply  
  Resume: ACCELERATOR_REDUCTION resumes from frozen value

EFFECTIVE_RATE calculation:

  best_rate = min(PROTOCOL_BASE_RATE, VAULT_RATE)  
  effective_rate = best_rate × (1 - nft_discount)

  nft_discount: Lite = 0.10, Core = 0.20, Maxi = 0.50, None = 0

---

## A4. Best-Case Effective Rate Matrix

At $100M AUM (base 7%), full 2pp accelerator (vault rate 5%):

  No NFT:         5.0%  
  Genesis Lite:   5.0% × 0.90 = 4.5%  
  Genesis Core:   5.0% × 0.80 = 4.0%  
  Genesis Maxi:   5.0% × 0.50 = 2.5%  ← best possible in system

At $15M AUM (base 9%), 1.5pp accelerator (vault rate 7.5%):

  No NFT:         7.5%  
  Genesis Core:   7.5% × 0.80 = 6.0%  
  Genesis Maxi:   7.5% × 0.50 = 3.75%

---

## A5. Non-Stable Asset Cap

NON_STABLE_TOTAL = BTC + ETH + BNB + XRP + SOL (vault allocation values)

CONSTRAINT: NON_STABLE_TOTAL <= 0.30 × vault_value  [INVARIABLE]

If AI output would breach cap:

  Safety Layer redistributes excess to stablecoin strategies  
  Logs anomaly, alerts admin dashboard  
  User not impacted — rebalance is silent

Stablecoins (USDT, USDC, DAI) excluded from cap calculation entirely

---

## A6. Arbitrage Pool — Profit Distribution

vault_share_pct = vault_arb_contribution / total_pool_value  
vault_epoch_arb_profit = total_pool_epoch_profit × vault_share_pct

Example:

  Total arb pool: $250,000  
  Vault A contribution: $500    share: 0.20%  
  Vault B contribution: $5,000  share: 2.00%  
  Pool epoch profit: $1,800  
  Vault A receives: $1,800 × 0.002 = $3.60  
  Vault B receives: $1,800 × 0.020 = $36.00

Exit mechanics:

  Standard exit: queued — vault share paid as positions close, max 48h  
  Urgent exit:   arb share settles separately within 48h, no urgency premium  
  Contribution snapshot: taken at epoch open for profit calculation

---

## A7. Deposit Credit Pool Distribution

Epoch_Pool = total_platform_fees_epoch × 0.05

Total_Shares = (active_Core_NFTs × 1.0) + (active_Maxi_NFTs × 2.5)

Credit_per_Share = Epoch_Pool / Total_Shares  
Core_credit_per_NFT = Credit_per_Share × 1.0  
Maxi_credit_per_NFT = Credit_per_Share × 2.5

Credits are wallet-level, not vault-level  
Apply to next deposit of $250 or more  
Cannot be withdrawn as USDT  
Expire after 12 months of inactivity

---

## A8. Protocol Pool Floors

Redemption pool: >= 105% of total_LOOP_outstanding × current_LOOP_value  [INVARIABLE]  
Ratchet pool:    >= 60_day_projected_redemption_demand  [INVARIABLE]

Ratchet can only fire when both pools are above floors  
Pool health checked at every epoch close and ratchet candidate event

---

## A9. LOOP Ratchet — Hard Quarterly Minimum

Standard conditions for ratchet:

  redemption_pool >= floor AND  
  ratchet_pool >= floor AND  
  hours_since_last_ratchet >= 72 AND  
  AI optimization model recommends increment

Hard quarterly minimum:

  IF days_since_last_ratchet >= 90 AND  
     redemption_pool >= floor AND  
     ratchet_pool >= floor:

  THEN ratchet MUST fire at next epoch close  
  Increment: minimum 0.25% (AI may recommend higher)

This overrides AI discretion — if 90 days pass and pools are healthy,  
the ratchet fires regardless of model output

---

## A10. Urgent Withdrawal Cost

Friction = gas_fees + DEX_exit_fees + LP_exit_fees +  
          slippage_estimate + staking_unbonding_penalty

Urgency_premium = Friction × 0.20  [minimum — INVARIABLE]

Total_exit_cost = Friction + Urgency_premium

User_receives ≈ vault_balance - Total_exit_cost

LOOP treatment:

  Fully issued past-epoch LOOP: SAFE — never forfeited  
  Current incomplete epoch LOOP: FORFEITED  
  Forfeiture value shown in dollars before user confirms

Arb pool portion:

  Settles separately within 48 hours  
  No urgency premium applied to arb settlement  
  Shown as separate line item in exit preview

---

## A11. Gas Reserve

AUTO-TOP-UP mode:

  Trigger: reserve < 0.005 BNB  
  TopUp = user_pct × settled_USDT  (default 0.02 = 2%)

AUTO-REBALANCE mode (recommended):

  Top-up trigger: reserve < 0.005 BNB  → convert USDT to BNB  
  Excess trigger: reserve > daily_cost × 30  → convert BNB to USDT  
  Net effect: reserve stays in optimal range automatically

---

## A12. Referral Credit Eligibility

Referral credit fires at T+30 days from new user's first deposit IF:

  new_wallet has no prior YieldLoop transaction history AND  
  referee vault has not been closed in the 30-day window AND  
  new_user has not been flagged as sybil (same device/IP cluster as referrer)

Credit: $5 deposit credit to BOTH wallets  
Type: deposit credit — not withdrawable as USDT  
Expiry: 12 months from issuance if unused  
One referral code per wallet — code is permanent and reusable

---

# Appendix B — Full Parameter Table

INVARIABLE = hard-coded in deployed contracts. Requires 5-of-5 multisig + re-audit + 30-day public notice to change.

| Parameter | Value |
|---|---|
| Min initial deposit | $1,000 USDT |
| Min subsequent deposit | $250 USDT |
| Base contribution cap | $100,000 [INVARIABLE] |
| Base profit cap | $100,000 [INVARIABLE] |
| Base total ceiling | $200,000 [INVARIABLE] |
| Core cap multiplier | 2× base = $400,000 [INVARIABLE] |
| Maxi cap multiplier | 5× base = $1,000,000 [INVARIABLE] |
| Launch fee rate | 12% [INVARIABLE] |
| Fee floor | 7% [INVARIABLE] |
| Fee direction | One-direction only — never increases [INVARIABLE] |
| AUM ratchet thresholds | $1M, $5M, $15M, $40M, $100M [INVARIABLE] |
| Vault accelerator max | 2pp [INVARIABLE] |
| Accelerator direction | One-direction only [INVARIABLE] |
| Non-stable cap | 30% of vault value [INVARIABLE] |
| Redemption pool floor | 105% of LOOP liability [INVARIABLE] |
| Ratchet pool floor | 60 days projected demand [INVARIABLE] |
| Ratchet interval minimum | 72 hours between events [INVARIABLE] |
| Ratchet increment range | 0.25%–2.0% [INVARIABLE] |
| Ratchet quarterly minimum | Once per 90 days when pools healthy [INVARIABLE] |
| Urgent premium minimum | 20% of friction [INVARIABLE] |
| Urgent per-request cap | 50% of vault [INVARIABLE] |
| Urgent AUM rate limit | 10% of total AUM per 24h [INVARIABLE] |
| Protocol pause maximum | 72 hours [INVARIABLE] |
| Overflow timelock | 48 hours [INVARIABLE] |
| Token whitelist notice period | 7 days before new token activates [INVARIABLE] |
| Multisig threshold — standard | 3-of-5 |
| Multisig threshold — invariables | 5-of-5 + re-audit + 30-day notice [INVARIABLE] |
| Oracle | Chainlink BSC — all tokens [INVARIABLE] |
| Lite NFT clock | Runs continuously from vault application — dormancy does not pause [INVARIABLE] |
| Audit requirement | Mandatory before mainnet [NON-NEGOTIABLE] |
| Bug bounty requirement | Mandatory before mainnet [NON-NEGOTIABLE] |
| Governance voter threshold | $10,000 total vault balance OR Genesis Core/Maxi NFT |
| Governance vote weight | One wallet = one vote |
| LoopLab funding | 10% of all NFT proceeds — permanent |
| LoopLab large grant threshold | $10,000 — requires governance vote |

---

# Appendix C — Token Basket v5.2

## C1. Spot & Arb Tokens — BSC Contract Addresses

BTCB:  0x7130d2A12B9BCbFAe4f2634d864A1Ee1Ce3Ead9c  
ETH:   0x2170Ed0880ac9A755fd29B2688956BD959F933F8  
WBNB:  0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c  
XRP:   0x1D2F0da169ceB9fC7B3144628dB156f3F6c60dBE  
SOL:   0x570A5D26f7765Ecb712C0924E4De545B89fD43dF

## C2. Stablecoin Addresses — BSC

USDT:  0x55d398326f99059fF775485246999027B3197955  
USDC:  0x8AC76a51cc950d9822D68b83fE1Ad97B32Cd580d  
DAI:   0x1AF3F329e8BE154074D8769D1FFa4eE058B1DBc3

Verify all addresses on BSCScan before deployment. XRP and SOL are Binance-wrapped BEP-20 tokens — different from native XRP and Solana chain assets. USDT and USDC on BSC differ from their Ethereum mainnet addresses.

## C3. Chainlink Price Feed Addresses — BSC

BTC/USD:  0x264990fbd0A4796A3E3d8E37C4d5F87a3aCa5Ebf  
ETH/USD:  0x9ef1B8c0E4F7dc8bF5719Ea496883DC6401d5b2e  
BNB/USD:  0x0567F2323251f0Aab15c8dFb1967E4e8A7D42aeE  
XRP/USD:  0x93A67931605d5954C1f7CC65c2d3fA55b5c72090  
SOL/USD:  0x0E8a53DD9f8a73404456571d71f3f6fC60E6d89f  
DAI/USD:  0x132d3C0B1D2cEa0BC552588063bdBb210FDeecfA  
USDT/USD: 0xB97Ad0E74fa7d920791E90258A6E2085088b4320  
USDC/USD: 0x51597f405303C4377E36123cBc172b13269EA163

Always verify feed addresses against official Chainlink BSC documentation before deployment. Feeds are occasionally updated.

## C4. Token Role Summary

| Token | Role |
|---|---|
| BTC (BTCB) | Spot |
| ETH | Spot |
| BNB | Spot |
| XRP (BEP-20) | Spot (PCS+BSW) |
| SOL (BEP-20) | Spot (PCS+BSW) |
| USDT | Pairs only |
| USDC | Pairs only |
| DAI | Pairs only |

## C5. Whitelist Expansion Criteria

- Minimum 90-day liquidity history on BSC
- Active Chainlink BSC price feed
- Minimum $5M 30-day average volume on PCS V3 or BiSwap
- Security review completed by protocol team
- 3-of-5 governance approval
- 7-day notice to all vault holders before activation
- 7-day opt-out window — vault holders may exclude new token from their strategy

---

# Appendix D — NFT Break-Even Analysis

## D1. Methodology

All break-even calculations use fee savings plus deposit credit pool distributions. Fee savings based on the vault's effective rate after accelerator at the given AUM scenario. Credit pool amounts based on Appendix I scenarios.

## D2. Genesis Lite — $150, 10% off, 13 months

At 12% base (launch, no accelerator):

  $10K profit/yr:  saves $120/yr  → payback 15 months (at window edge)  
  $15K profit/yr:  saves $180/yr  → payback 10 months ✓  
  $20K profit/yr:  saves $240/yr  → payback 7.5 months ✓

At 10% base ($5M AUM), 1pp accelerator (vault rate 9%):

  $15K profit/yr:  saves $135/yr  → payback 13.3 months (tight)  
  $25K profit/yr:  saves $225/yr  → payback 8 months ✓

Note: Lite clock runs from vault application — dormancy does not stop it.

Best value for active vaults expecting $15K+ annual profits.

## D3. Genesis Core — $1,000, 20% off, Lifetime

At $15M AUM (base 9%), 1.5pp accel (vault rate 7.5%), Core → 6.0%:

  $25K profit/yr:  saves $375/yr + $67/mo credits = $1,179/yr → payback 10.2 months  
  $40K profit/yr:  saves $600/yr + $67/mo credits = $1,404/yr → payback 8.5 months

At $40M AUM (base 8%), 2pp accel (vault rate 6%), Core → 4.8%:

  $40K profit/yr:  saves $480/yr + $200/mo credits = $2,880/yr → payback 4.2 months

Lifetime validity — break-even improves continuously as protocol grows.

## D4. Genesis Maxi — $3,000, 50% off, Lifetime

At $15M AUM (base 9%), 2pp accel (vault rate 7%), Maxi → 3.5%:

  $40K profit/yr:  saves $1,400/yr + $167/mo credits = $3,404/yr → payback 10.6 months  
  $80K profit/yr:  saves $2,800/yr + $167/mo credits = $4,804/yr → payback 7.5 months

At $100M AUM (base 7%), 2pp accel (vault rate 5%), Maxi → 2.5%:

  $40K profit/yr:  saves $1,000/yr + $500/mo credits = $7,000/yr → payback 5.1 months  
  $80K profit/yr:  saves $2,000/yr + $500/mo credits = $8,000/yr → payback 4.5 months

Scarcity: 300 public Maxi supply. Secondary market premiums expected.

---

# Appendix E — Vault Accelerator Scoring Detail

SIGNALS AND APPROXIMATE WEIGHTS:

  Balance:           ~30%  Absolute vault value — larger = higher score  
  Deposit Volume:    ~25%  Total USDT deposited in trailing 6 months  
  Deposit Frequency: ~25%  Count of deposits in trailing 6 months  
                          Regular cadence scores higher than lump-sum deposits  
  Deposit Quality:   ~20%  Composite of three sub-signals:

    Consistency:           Deposits arriving at regular intervals  
    Net Positive Trend:    More flowing in than out over 6 months  
    Low Withdrawal Freq:   Fewer partial withdrawals = higher quality

OUTPUT:

  accelerator_reduction ∈ [0.0, 0.02]  (0 to 2 percentage points)

CONSTRAINTS (Safety Layer):

  new_reduction >= current_reduction at all times  
  Dormancy: reduction freezes, resumes from same level on return  
  Multi-vault: each vault scored independently  
  Governance: accelerator does not affect governance eligibility calculation  
    (governance uses total wallet balance, not accelerator state)

---

# Appendix F — Strategy Allocation Ranges by Risk Tier

CONSERVATIVE:

  Venus Lending:     15–25%  (USDT, USDC, DAI)  
  BNB Staking:       30–45%  (stkBNB, ankrBNB)  
  Stable LP:         15–25%  (USDT-USDC, DAI-USDT, DAI-USDC)  
  Spot Trading:      10–20%  (BTC, ETH, BNB primary — XRP/SOL only if vault >= $3K)  
  Arb Pool:          0–10%   (only if vault arb contribution >= $300)  
  Gas Reserve:       ~1.5%

BALANCED:

  Venus Lending:     10–18%  
  BNB Staking:       20–30%  
  Mixed LP:          18–28%  (stable pairs + BNB pairs + XRP/SOL pairs if vault supports)  
  Spot Trading:      20–30%  (all 5 tokens)  
  Arb Pool:          10–20%  
  Gas Reserve:       ~1.5%

GROWTH:

  Venus Lending:      5–12%  
  BNB Staking:       10–20%  
  Mixed LP:          12–22%  
  Spot Trading:      30–40%  (full basket, approaching 30% non-stable cap)  
  Arb Pool:          15–30%  
  Gas Reserve:       ~1.5%

All allocations subject to 30% non-stable cap [INVARIABLE]

AI adjusts within these ranges based on market conditions and vault size

---

# Appendix G — Income Stream Availability by Vault Size

Strategy eligibility is vault-driven. No AUM gates.

AI applies judgment — these are guidelines, not hard locks.

$1,000 vault:

  Venus Lending:    ✓ All three stablecoins  
  BNB Staking:      ✓  
  Stable LP:        ✓ if allocation >= $500  
  Spot Trading:     ✓ Even small positions are valid  
  Arb Pool:         ✓ if contribution >= $300 (30% of $1K vault)  
  XRP/SOL spot:     ✓ AI may allocate small positions  
  Mixed LP:         Limited — gas efficiency marginal at this size

$5,000 vault:

  All strategies fully available  
  XRP/SOL LP:       ✓ at Balanced/Growth tier  
  Arb Pool:         ✓ meaningful contribution

$15,000+ vault:

  All strategies fully available at meaningful scale  
  AI has full allocation flexibility across all 8 tokens  
  Multi-vault strategy becomes attractive at this level

---

# Appendix H — Vault Overflow Logic

Soft trigger (90% of ceiling): AI notifies user — confirm overflow routing  
Hard ceiling (100%):            Auto-fires to elected destination  
No destination elected:         Funds held in suspense — user has 72h to set routing  
After 72h with no action:       Auto-routes to system vault contribution

Overflow destination options:

  External wallet  
  Auto-create new vault (standard settings)  
  Top up an existing vault  
  System vault contribution

Destination change timelock: 48 hours [INVARIABLE]

---

# Appendix I — Credit Pool Scenarios

Assumptions: 400 active Core NFTs, 150 active Maxi NFTs

Total shares = (400 × 1.0) + (150 × 2.5) = 775 shares

AUM     Fee%   Monthly Fees   Pool(5%)   Per Share   Core/mo    Maxi/mo

$1M     12%    $10,000        $500       $0.65       $0.65      $1.61  
$5M     10%    $41,667        $2,083     $2.69       $2.69      $6.72  
$15M     9%    $112,500       $5,625     $7.26       $7.26      $18.15  
$40M     8%    $266,667       $13,333    $17.20      $17.20     $43.01  
$100M    7%    $583,333       $29,167    $37.63      $37.63     $94.08

All figures estimated. Actual depends on vault activity and NFT holder count.

---

# Appendix J — Epoch and Ratchet Log Format

Per-epoch public record (published at epoch close):

Epoch:              Number, start date, end date  
Total LOOP Issued:  Amount, opening rate, closing rate  
Ratchet Events:     Count, dates, increments, pool states at each  
Protocol AUM:       Total across all chains at epoch close  
Protocol Base Rate: At epoch close — any drops recorded  
Vault Accelerators: All vault rates (anonymised) — distribution shown  
Fees Collected:     Total, average effective rate across all vaults  
Credit Pool:        Total distributed, per Core, per Maxi  
System Vault:       Balance, earnings, pool contribution  
Total LOOP Outstanding: Cumulative, current redemption value  
Quarterly Ratchet Check: Days since last ratchet — flag if >= 90 and pools healthy

---

# Appendix K — Tax Export CSV Format

Available any time from the transaction history page. Filter by date range. No minimum or maximum period.

CSV Field | Description
---|---
date_utc | ISO 8601 timestamp — e.g. 2025-03-15T14:23:11Z
transaction_type | Deposit \| Withdrawal \| USDT_Claim \| LOOP_Issuance \| LOOP_Redemption \| Fee_Deduction \| Credit_Applied \| NFT_Purchase \| Referral_Credit \| Arb_Settlement
token_in | Token received — USDT, LOOP, BNB, etc.
amount_in | Quantity received — decimal precision
token_out | Token sent — USDT, BNB (gas), etc.
amount_out | Quantity sent — decimal precision
usd_value_at_time | USD value at execution via Chainlink price — key field for cost basis
fee_paid_usdt | Platform fee deducted this transaction, if any
running_balance_usdt | Vault USDT balance after transaction
vault_id | Which vault — supports multi-vault filtering
tx_hash | On-chain transaction hash for independent verification
notes | Human-readable summary — e.g. 'Stop-loss BTC position closed at $42,150'

This format covers the data requirements for cost basis calculation (FIFO, LIFO, AVCO), income reporting for yield/interest, and capital gains reporting. Users should consult a tax professional for their specific jurisdiction — YieldLoop provides data only, not advice.

---

# Appendix L — Glossary

Term | Definition
---|---
AUM Ratchet | Automatic on-chain mechanism that drops the protocol base rate when total AUM crosses milestones. Permanent and irreversible.
Vault Accelerator | Each vault's earned personal rate reduction — up to 2pp below current protocol base rate. Based on activity signals.
Best Available Rate | The lower of: protocol base rate or vault's accelerated rate. NFT discount is applied to this.
Effective Rate | Best available rate after NFT discount. The actual fee percentage applied to vault profits.
Deposit Quality | Accelerator signal: consistency of deposits + net positive trend + low withdrawal frequency.
Dormancy | No qualifying activity in 30 days. Accelerator pauses at earned rate. Protocol drops still apply. Resumes on return.
One-Direction Ratchet | Core protocol mechanic: fees can only decrease. Hard-coded prohibition on any increase.
Non-Stable Cap | 30% of vault value maximum in BTC + ETH + BNB + XRP + SOL combined. INVARIABLE.
Token Basket | Eight launch tokens: BTC, ETH, BNB, XRP, SOL (spot/arb) + USDT, USDC, DAI (stable).
Whitelist | Governance-controlled list of tokens eligible for spot trading. Expandable via 3-of-5 vote.
BTCB | Binance-pegged Bitcoin BEP-20 token on BSC — tracks Bitcoin price.
BEP-20 | BSC token standard. XRP and SOL trade as BEP-20 wrapped versions on BSC.
LOOP | Non-transferable internal accounting unit. Issued at $1.00. Appreciates via ratchet. Not a security.
Epoch | One calendar month — LOOP issuance, accelerator scoring, credit pool distribution cycle.
Ratchet Event | A permanent increase to the LOOP redemption rate — 0.25% to 2.0% per event. Never reverses.
Quarterly Minimum | Hard protocol commitment: ratchet fires at least once per 90 days when pools are healthy.
Genesis Lite | $150 — 10% off — 13 months from vault application — clock always runs.
Genesis Core | $1,000 — 20% off — lifetime — $400K ceiling — 1.0 credit share.
Genesis Maxi | $3,000 — 50% off — lifetime — $1M ceiling — 2.5 credit shares.
INVARIABLE | Hard-coded in deployed contracts. Requires 5-of-5 multisig + re-audit + 30-day notice to change.
Venus Protocol | BSC lending — YieldLoop uses supply-only for USDT, USDC, DAI. No borrowing.
Transak | Regulated fiat-to-crypto onramp integrated into the YieldLoop dapp for USDT purchase.
Privy | Wallet creation service — enables new users to create a BSC wallet with just email or social login.
PCS / PancakeSwap | Primary DEX on BSC — PancakeSwap V3. Primary spot trading and LP venue.
BiSwap (BSW) | Secondary DEX on BSC. Used for best-execution routing alongside PCS.
Best Execution | AI compares PCS and BiSwap prices before every spot trade — routes to better price.
Stop-Loss | AI-triggered autonomous sell of a position when price drops to threshold. Executes immediately, user notified after.
Arb Pool | Shared protocol cross-DEX arbitrage pool. Vaults contribute allocation, receive proportional profits.
Queued Exit | Arb pool exit mechanism — vault's share paid out as positions close naturally, max 48 hours.
Hub Chain | BSC — permanent home of LOOP, pools, governance, and fee ratchet.
Spoke Chain | Future deployment chain (Ethereum, Base, Arbitrum, Solana) — vaults only, accounting flows to BSC.
Governance Voter | Wallet with $10K+ total vault balance OR Genesis Core/Maxi NFT holder. One wallet = one vote.
LoopLab | Independent organization funded by 10% of NFT proceeds. Technology education and uplift mission.
Proof of Impact | LoopLab's outcome measurement framework — grants require measurable results to continue.
Tax Export | Universal CSV of all taxable events — available any time, filterable by date range.

	YieldLoop Protocol v5.2 — Confidential
