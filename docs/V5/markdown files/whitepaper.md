YieldLoop — Whitepaper  |  v5.2

# YIELDLOOP

## Whitepaper

### Protocol Design, Economics, Architecture & LoopLab Vision | v5.2 — BNB Smart Chain

---

FOR INFORMATIONAL PURPOSES ONLY. NOT AN OFFER OF SECURITIES OR FINANCIAL INSTRUMENTS. DIGITAL ASSET INVESTMENTS INVOLVE SUBSTANTIAL RISK INCLUDING COMPLETE LOSS OF PRINCIPAL.

SECURITY COMMITMENT: YieldLoop will not deploy to mainnet without a completed independent audit and active bug bounty. All reports published publicly before launch.

---

# Abstract

YieldLoop is a non-custodial yield optimization protocol on BNB Smart Chain. It aggregates five DeFi income streams into isolated per-user vaults governed by a conversational AI recommendation engine and a one-direction fee ratchet that starts at 12% and can only ever decrease.

As the protocol grows, fees fall automatically for every vault. As individual vaults demonstrate consistent quality activity, they earn additional personal rate reductions. The two effects compound. The starting token basket — BTC, ETH, BNB, XRP, SOL for spot trading and arbitrage; USDT, USDC, DAI as stablecoins — provides broad market exposure with deep BSC liquidity. Strategy eligibility is determined per-vault by the AI, not by protocol-wide AUM milestones.

The protocol is designed from day one to be multi-chain. BSC is the permanent hub for all LOOP accounting, pools, and governance. Spoke deployments on Ethereum, Base, Arbitrum, and Solana are planned as the protocol matures. YieldLoop exists to generate yield for its users and, through LoopLab, to direct a portion of that growth toward uplifting underserved communities through technology education and opportunity.

---

# 1. Design Principles

Non-custodial — users retain cryptographic control at all times. YieldLoop cannot access, freeze, or recover user funds.

Fees only go down — the one-direction ratchet is a hard protocol commitment enforced in smart contract code, not policy.

Earned improvement — vaults that demonstrate quality activity reach lower rates faster through the vault accelerator.

AI-assisted, not AI-controlled — all recommendations require explicit user acknowledgment. The AI cannot override invariables.

Conservative by default — capital protection takes priority over yield maximization. Users choose their own risk tier.

Strategy eligibility is vault-driven — the AI decides what each vault can use based on vault size and health, not protocol AUM milestones.

Transparent — fee rates, accelerator scores, AI reasoning, pool states, and ratchet history all visible to users at all times.

Multi-chain aware — architecture designed for BSC-hub, multi-spoke expansion without a protocol rebuild.

Audited before launch — no mainnet without completed third-party audit and active bug bounty. Non-negotiable.

Purpose-driven — 10% of NFT proceeds permanently fund LoopLab, a real-world mission to uplift communities through technology.

---

# 2. Fee Architecture — The One-Direction Ratchet

## 2.1 Core Promise

The platform fee on vault profits starts at 12% at protocol launch and can only ever decrease. It is structurally impossible for the fee to increase — enforced in smart contract code, not just policy. Every milestone crossed is permanent. No governance vote, no multisig, no emergency action can reverse a fee drop.

## 2.2 Protocol Base Rate — AUM Ratchet

As total protocol AUM crosses each milestone, the base rate drops automatically for every vault simultaneously. No action required from users.

Protocol AUM Milestone

Protocol Base Rate

Launch

12%

Crosses $1M AUM

Drops to 11% — automatic

Crosses $5M AUM

Drops to 10% — automatic

Crosses $15M AUM

Drops to 9% — automatic

Crosses $40M AUM

Drops to 8% — automatic

Crosses $100M AUM

Drops to 7% — permanent floor

## 2.3 Vault Accelerator

Each vault earns up to 2 percentage points below the current protocol base rate through four activity signals: vault balance, deposit volume, deposit frequency, and deposit quality (consistency, net positive trend, and low withdrawal frequency). One-direction only. Pauses during dormancy — the earned rate holds, it does not reverse. Resumes from the same level when activity returns.

Signal

Weight (approx)

Vault Balance

~30%

Deposit Volume

~25%

Deposit Frequency

~25%

Deposit Quality

~20%

## 2.4 NFT Discounts

Applied to whichever is lower: the protocol base rate or the vault's earned accelerated rate.

NFT Tier

Discount

No NFT

None

Genesis Lite

10% off best rate

Genesis Core

20% off best rate

Genesis Maxi

50% off best rate

## 2.5 LOOP Ratchet — Hard Quarterly Minimum

The LOOP ratchet permanently increases the redemption value of all outstanding LOOP. Increments range from 0.25% to 2.0% per event. The ratchet fires when both protocol pools are above their floor levels and at least 72 hours have passed since the last event.

Hard commitment: the ratchet fires at minimum once per calendar quarter when both pools are above their floors. If 90 days pass and both pools are healthy, a ratchet event fires at the next epoch close regardless of AI recommendation. This is a protocol commitment, not a discretionary target.

---

# 3. Token Basket

## 3.1 Launch Basket — Eight Tokens

Token

Type

BTC (BTCB)

Crypto

ETH

Crypto

BNB

Native

XRP

Crypto

SOL

Crypto

USDT

Stablecoin

USDC

Stablecoin

DAI

Stablecoin

## 3.2 Non-Stable Cap

A maximum of 30% of any vault's value may be held across all non-stable assets combined (BTC + ETH + BNB + XRP + SOL). This is a hard invariable enforced by the Safety Layer — the AI cannot override it. The remaining 70%+ is always in stablecoins.

## 3.3 Whitelist Expansion

New spot trading tokens are added via governance vote. Requirements: minimum 90-day BSC liquidity history, active Chainlink BSC price feed, minimum $5M 30-day average volume on PCS or BiSwap, security review by protocol team, 3-of-5 governance approval. Existing vault holders receive 7 days notice before a new token activates in their vault, with an opt-out window.

---

# 4. Strategy Eligibility — Vault-Driven, Not AUM-Driven

There are no protocol-wide AUM thresholds that lock or unlock strategies. The AI evaluates each vault individually and recommends the strategy mix that fits that vault's size, risk tier, and current market conditions. A $5,000 vault can run spot trading, staking, lending, LP, and arb contributions from day one if the AI determines those strategies are appropriate for it.

## 4.1 Strategy Availability by Vault Size (AI Guidelines)

Strategy

Practical Minimum Vault Contribution

Venus Lending (USDT/USDC/DAI)

Any size

BNB Staking (stkBNB/ankrBNB)

Any size

Liquidity Provision (stable pairs)

$500 in strategy

Spot Trading (all 5 tokens)

Any meaningful position

Cross-DEX Arbitrage (pool)

$300 vault contribution

LP Mixed Pairs (BTC/ETH/BNB)

$1,000 in strategy

These are AI guidelines, not hard contract locks. The AI applies judgment based on vault size, risk tier, gas costs, and current pool depth. A $1,000 Conservative vault will have minimal spot exposure. A $1,000 Growth vault may have significantly more. The AI decides — the user acknowledges.

## 4.2 Arb Pool Mechanics

The cross-DEX arbitrage strategy uses a shared protocol-level pool. Individual vaults contribute an allocation and receive profits proportional to their contribution share of the total pool.

Profit distribution: Vault's contribution as a percentage of total pool value at epoch close

Entry: AI allocates vault funds to pool during rebalance — no separate user action

Exit — standard closure: Queued exit — vault's share paid out as positions close naturally, maximum 48 hours

Exit — immediate vault exit: Arb share settles separately within 48 hours — no urgency premium applied to this portion

Pool health input: AI monitors pool depth and only allocates to arb when the pool is healthy enough for meaningful returns

---

# 5. Income Streams

Spot Trading: Take-profit and stop-loss automation on PCS V3 and BiSwap. All 5 non-stable tokens eligible. Best execution routing. Stop-loss executes autonomously — user notified after.

Cross-DEX Arbitrage: Statistical and latency arbitrage between PCS and BiSwap. All 8 tokens eligible. Shared protocol pool — proportional profit distribution.

Liquidity Provision: LP positions via Beefy or Krystal. Stable-stable, stable-BNB, and DAI pairs. XRP/SOL LP at Balanced and Growth tiers when vault size supports it.

Token Staking: stkBNB (Stader) and ankrBNB (Ankr) for liquid staking with immediate secondary exit. Conservative anchor strategy.

Venus Lending: Supply-only. No borrowing. No liquidation risk. Immediate redemption. USDT, USDC, and DAI all active lending markets.

---

# 6. Vault Architecture

## 6.1 Core Parameters

Minimum initial deposit: $1,000 USDT

Minimum subsequent deposit: $250 USDT

Base contribution cap: $100,000

Base profit cap: $100,000

Base total ceiling: $200,000

Genesis Core cap: $400,000 total ceiling (2x base)

Genesis Maxi cap: $1,000,000 total ceiling (5x base)

USDT settlement: Continuous — claimable any time as trades close

LOOP settlement: Monthly at epoch close

## 6.2 Multi-Vault Strategy

Users may operate multiple vaults simultaneously. This is intentional and supported. Each vault is independent — its own risk tier, its own NFT, its own accelerator score, its own strategy. Common use cases:

Different risk tiers — Conservative vault for capital preservation, Growth vault for higher exposure

NFT tier optimization — Core on one vault, Maxi on another if both exceed $100K

Overflow routing — Vault 1 overflow routes to Vault 2 automatically

Strategy separation — one vault for stable yield, one for active spot trading

Fee optimization — each vault earns its own accelerator independently

Governance rights are calculated per-wallet across all vaults combined. A wallet with $6,000 in Vault 1 and $5,000 in Vault 2 qualifies for governance rights ($11,000 combined). One wallet = one vote.

## 6.3 Gas Reserve

Manual: User tops up BNB manually when notified

Auto-Top-Up: 1–10% of profits (default 2%) converted to BNB automatically

Auto-Rebalance (recommended): Tops up when reserve is low, converts excess BNB back to USDT

## 6.4 Overflow Routing

External wallet

Auto-create new vault

Top up existing vault

System vault contribution

Overflow destination changes carry a 48-hour timelock.

---

# 7. AI Recommendation System

Optimization Model: XGBoost-class. Strategy allocations across 8-token basket, pool targets, vault accelerator scoring, stop-loss and take-profit thresholds.

Safety Layer: Deterministic rules. Enforces one-direction fee rule, 30% non-stable cap, all invariables. Cannot be bypassed by any model output.

Communication Layer: Gemini Flash 2.0. Conversational goal-setting, fee trajectory, plain-language summaries. Fallback: Claude Haiku / GPT-4o Mini.

## 7.1 Stop-Loss Execution

When the AI determines a stop-loss threshold has been reached, execution is autonomous and immediate. The AI acts without waiting for user confirmation — waiting defeats the purpose in fast-moving markets. The user is notified immediately after execution with full trade details, the reason for the stop-loss trigger, and the resulting position. Users set their own stop-loss thresholds in Advanced Mode. Once set, execution is automatic.

---

# 8. LOOP Accounting Unit

LOOP is a non-transferable, non-tradeable internal accounting unit. It is issued at $1.00 per LOOP at epoch close and appreciates over time through ratchet events. It is not a security, not a cryptocurrency, and has no value outside the YieldLoop protocol.

Issuance: 1:1 with USDT at epoch close for LOOP-elected profits

Ratchet: Permanently increases redemption rate by 0.25%–2.0% per event — never reverses

Hard minimum: At least one ratchet per quarter when both pools are above floors

Redemption: Any time — current ratcheted value paid in USDT from redemption pool

Forfeiture on immediate exit: Current incomplete epoch LOOP only — all fully issued past-epoch LOOP is always safe

Total forfeiture: Only if user elects USDT-only profits going forward — future LOOP stops accumulating, existing LOOP safe

---

# 9. Protocol Pools

Redemption pool floor: 105% of total LOOP liability [INVARIABLE]

Ratchet pool floor: 60 days projected demand [INVARIABLE]

System vault: Overflow → suspense → auto-creates system vault at $1,000 → earnings feed pools 100%

Pool funding: 65% of protocol fee revenue allocated dynamically by AI between pools

---

# 10. Withdrawal Mechanics

USDT claims: Any time — continuous settlement, no minimum, no delay

LOOP redemption: Any time — current ratcheted value paid immediately in USDT

Standard closure: 5+ days notice before month-end — no penalty, no urgency fee

Immediate withdrawal: Friction costs + 20% urgency premium on friction — all current epoch LOOP forfeited, all past LOOP safe

Arb portion on immediate exit: Settles separately within 48 hours — no urgency premium on this portion

---

# 11. Referral Program

Every wallet that holds an active vault automatically receives a referral code. When a new user applies that code at signup, both the referrer and the new user receive a $5 USDT deposit credit exactly one calendar month after the new user's first deposit.

Eligibility: New wallet must have no prior YieldLoop history. Referee must not have closed their vault in the 30-day window.

Credit type: Deposit credit — applies to next deposit of $250 or more. Cannot be withdrawn as USDT.

Anti-sybil: One referral code per wallet. Wallets with prior YieldLoop history cannot use a referral code.

Credit expiry: 12 months of inactivity — consistent with all deposit credits

---

# 12. NFT Program

Genesis Lite

Price: $150 USDT

Supply: Rolling batches: 3,000 public + 333 team per batch

Validity: 13 months from vault application date

Clock rule: Once applied to a vault, the 13-month clock runs continuously. Dormancy does not pause it. Invariable.

Discount: 10% off best available rate

Vault ceiling: $200,000 (standard — no change)

Credit pool: No access

Genesis Core

Price: $1,000 USDT

Supply: 1,111 total: 1,000 public + 111 team/promotional (never expire)

Validity: Lifetime

Discount: 20% off best available rate

Vault ceiling: $400,000 total ceiling

Credit pool: 1.0 share per epoch

Secondary market: Yes — transferable until vault application

Genesis Maxi

Price: $3,000 USDT

Supply: 333 total: 300 public + 33 team/promotional (never expire)

Validity: Lifetime

Discount: 50% off best available rate

Vault ceiling: $1,000,000 total ceiling

Credit pool: 2.5 shares per epoch

Secondary market: Yes — genuine scarcity, secondary premiums expected

---

# 13. Deposit Credit Pool

Funding: 5% of all platform fees per epoch

Core share: 1.0 per active Core NFT per epoch

Maxi share: 2.5 per active Maxi NFT per epoch

Usage: Applies to next deposit of $250 or more. Cannot be withdrawn as USDT.

Expiry: 12 months of inactivity

---

# 14. Governance

YieldLoop uses a multisig governance model at launch, with a pathway toward broader on-chain governance as the protocol matures and the community grows.

## 14.1 Qualified Voters

A wallet qualifies for governance rights if it meets either of the following criteria:

$10,000 or more in total vault balance across all vaults associated with that wallet, OR

Holds a Genesis Core or Genesis Maxi NFT (bound or unbound)

One wallet = one vote, regardless of balance size above the threshold. A wallet with $500,000 across three vaults has the same voting weight as a wallet with $10,001 in one vault.

## 14.2 Voting Scope

Fee parameter changes (within invariable bounds)

Token whitelist additions — new spot trading tokens

Protocol upgrades and new strategy integrations

LoopLab grant allocations above $10,000

Multi-chain deployment decisions

## 14.3 Multisig Structure

Standard decisions: 3-of-5 multisig

Invariable changes: 5-of-5 multisig + re-audit + 30-day public notice [INVARIABLE]

Emergency pause: Single designated emergency key for pause only — see Section 16

## 14.4 Governance Evolution

BSC is the permanent governance hub. As the protocol grows, the team intends to transition toward broader on-chain governance. LOOP is not the governance token — it is an internal yield accounting unit. Future governance token design, if any, will be subject to its own community process and legal review. The multi-chain architecture is designed so that governance decisions made on BSC propagate to all spoke chains.

---

# 15. Multi-Chain Architecture

YieldLoop launches on BNB Smart Chain and is architected from day one for multi-chain expansion. BSC is not a temporary starting point — it is the permanent protocol hub. All LOOP accounting, pool balances, governance, and fee ratchet state live on BSC and never move.

## 15.1 Hub and Spoke Model

BSC Hub: LOOP issuance and redemption, protocol pools, governance, fee ratchet, AUM tracking, NFT registry

Spoke chains (planned): Ethereum mainnet, Base, Arbitrum, Solana

Spoke functionality: Vault creation and management, local DEX integrations, native token staking

Cross-chain accounting: Vault activity on spoke chains reports to BSC hub for AUM tracking, accelerator scoring, and LOOP issuance

User experience: Users on any chain see a unified dashboard — their vaults, their LOOP, their rate — regardless of which chain the vault lives on

## 15.2 Design Decisions Made Now for Multi-Chain

AUM tracking is chain-agnostic — the ratchet contract aggregates across all chains

Vault accelerator scoring is per-wallet, not per-chain

NFT discounts apply regardless of which chain the vault is on

Governance rights calculated across all chains combined

No timeline committed for spoke deployments — but no architectural changes needed when they happen

---

# 16. Emergency Procedures

## 16.1 Protocol Pause

The protocol can be paused for a maximum of 72 hours. This is a hard invariable — no pause can exceed 72 hours without a full multisig vote to extend, which itself requires public notice.

Who can trigger: Single designated emergency key OR 3-of-5 multisig

Emergency key scope: Pause only — cannot move funds, change parameters, or modify contracts

Multisig scope: Pause + can initiate orderly wind-down of specific strategies during pause

Maximum duration: 72 hours [INVARIABLE]

## 16.2 What Happens During a Pause

All new vault deposits suspended

All new withdrawals suspended — existing withdrawal requests in queue continue to process

Open spot positions: held, not closed — no forced exit during pause

LP positions: remain in pools — no forced exit during pause

Staking positions: remain staked — unbonding not triggered

Venus lending: remains active — supply positions continue to earn

Arb pool: no new positions opened — existing positions held

LOOP ratchet: suspended — epoch timer pauses

Gas reserve auto-rebalance: suspended

## 16.3 Communication Requirements

Pause trigger: public announcement within 15 minutes on all protocol channels

Status update: every 6 hours during pause

Resolution announcement: immediately upon resume or extension vote

Post-mortem: published within 7 days of any pause exceeding 24 hours

## 16.4 Resolution

Resume: 3-of-5 multisig vote — all suspended operations resume in order

Extension beyond 72 hours: 5-of-5 multisig + public notice [INVARIABLE]

Orderly wind-down: 5-of-5 multisig + 30-day notice to all vault holders [INVARIABLE]

---

# 17. Wallet Access, Death & Incapacitation

YieldLoop is non-custodial. This means users have complete control — and complete responsibility. The protocol has no mechanism to recover wallets, identify owners, designate beneficiaries, or act on instructions from third parties.

## 17.1 What YieldLoop Cannot Do

Recover a lost seed phrase or private key

Identify the owner of a wallet address

Transfer vault ownership to a beneficiary or estate

Accept instructions from family members, lawyers, or executors

Freeze or hold a vault pending estate proceedings

Provide any form of access to a vault without the controlling wallet

## 17.2 Recommended Precautions

Store seed phrase on paper in a physically secure location — not digitally

Consider a hardware wallet (Ledger, Trezor) for significant vault balances

Consider a personal multi-sig setup where access requires 2-of-3 trusted keys

Include wallet access instructions and seed phrase location in a sealed document with your estate planning materials

Inform a trusted person that a YieldLoop vault exists and where recovery materials are stored — without giving them the phrase directly

If access to the controlling wallet is lost — whether through death, incapacitation, device loss, or forgotten credentials — the vault and all its contents may be permanently inaccessible. This is the nature of non-custodial finance. The acknowledgment at vault creation confirms the user understands this.

---

# 18. Tax Reporting

YieldLoop does not provide tax advice and cannot determine the tax treatment of any transaction in any jurisdiction. Tax law for digital assets varies significantly by country and is evolving rapidly.

To support users in meeting their tax obligations, YieldLoop provides a universal CSV export from the transaction history page. This export contains the raw data needed by any accountant or tax software to calculate obligations under any country's framework.

## 18.1 CSV Export — Fields

Field

Description

Date / Time (UTC)

Timestamp of the transaction

Transaction Type

Deposit, Withdrawal, USDT Claim, LOOP Issuance, LOOP Redemption, Fee Deduction, Deposit Credit Applied, NFT Purchase, Referral Credit

Token In

Token received (e.g. USDT, LOOP)

Amount In

Quantity received

Token Out

Token sent (e.g. USDT, BNB for gas)

Amount Out

Quantity sent

USD Value at Time

USD value of the transaction at execution (Chainlink price)

Fee Paid (USDT)

Platform fee deducted, if applicable

Running Vault Balance (USDT)

Vault USDT balance after transaction

Transaction Hash

On-chain reference for verification

Users can filter by date range and export for any period. The export is available any time from the transaction history page. No account closure or special request required.

---

# 19. LoopLab — Proof of Impact

YieldLoop was built to generate yield. But yield without purpose is just numbers. LoopLab is the founders' answer to a question they couldn't stop asking: what happens when you give people who've been left behind by the technology economy a real way in?

## 19.1 The Mission

LoopLab is an independent organization funded permanently by 10% of all YieldLoop NFT proceeds. Its mission is to uplift underserved communities through hands-on training and education in the technologies that are reshaping the economy.

The curriculum is practical and forward-looking: fintech and digital financial literacy, Web3 and blockchain fundamentals, mobile and web application development, automation and workflow technology, robotics and physical computing, drone operation and applications. Not theory — skills that translate directly into employment, freelance income, and entrepreneurship.

LoopLab also serves local businesses. Small business owners can bring their operation to LoopLab and ask a simple question: how do I make this better with technology? LoopLab's team helps them find practical answers — not enterprise software they can't afford, but accessible tools and automation that fit a small business reality.

## 19.2 The Model

LoopLab operates as a grant-funded non-profit with an Executive Director responsible for the organization's day-to-day operations, program quality, grant administration, and financial sustainability. The Executive Director reports publicly on outcomes and ensures that LoopLab's activities remain aligned with its mission.

Funding comes from two sources: YieldLoop's permanent 10% NFT proceeds allocation, and external grants and partnerships that the Executive Director actively pursues. LoopLab is designed to grow beyond its YieldLoop funding — the protocol is the foundation, not the ceiling.

## 19.3 Proof of Impact

LoopLab operates under a Proof of Impact framework. Grant allocations above $10,000 require a governance vote from qualified YieldLoop protocol participants. Every program that receives funding must report measurable outcomes: students trained, businesses assisted, employment outcomes, projects launched. Funding continues for programs that demonstrate impact. It stops for programs that don't.

This is not charity for charity's sake. It is the belief that technology access is a lever — and that when you pull it for people who've never had the opportunity, remarkable things happen. YieldLoop users are not just earning yield. They are, through the protocol's structure, directly participating in that.

## 19.4 The Connection

Every Genesis NFT minted funds LoopLab. Every fee collected at protocol scale contributes to the pool that funds it. The more the protocol grows, the more LoopLab can do. This is intentional. The protocol's commercial success and its social mission are the same thing.

---

# 20. Fee Allocation

Allocation

Percentage

Protocol Pools (dynamic)

65%

Development & Operations

10%

Marketing & Growth

10%

LoopLab

10%

Deposit Credit Pool

5%

NFT proceeds have a separate allocation: 10% to LoopLab, 90% to protocol treasury. The system vault (overflow → auto-created) earns yield and directs 100% of its earnings to the redemption and ratchet pools — no fee allocation, no credit pool, no NFT discount.

---

# 21. Security Commitment

No mainnet deployment without:

1. Completed independent third-party audit — all critical and high severity findings resolved and verified

2. Active bug bounty program live before mainnet — minimum 2 weeks ahead of deployment

3. All audit reports published publicly in full

4. 30-day testnet deployment with community testing

This is non-negotiable. It is written into the protocol's founding documents and the team's public commitment to users.

---

# 22. Disclaimers

This document is for informational purposes only. It does not constitute an offer of securities, investment advice, or financial instruments of any kind. YieldLoop Ltd. is incorporated in Seychelles. LOOP is an internal accounting unit — it is not a cryptocurrency, not a security, and has no value outside the YieldLoop protocol. Digital asset investments involve substantial risk including complete loss of principal. Past performance of any yield strategy is not indicative of future results. Users are solely responsible for determining the legality of using this protocol in their jurisdiction and for meeting any applicable tax obligations.

	YieldLoop Protocol v5.2 — Confidential
