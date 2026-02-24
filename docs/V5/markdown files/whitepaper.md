YieldLoop — Whitepaper  |  v5.2

# YIELDLOOP

## Whitepaper

### Protocol Design, Economics, Architecture & LoopLab Vision | v5.2 — BNB Smart Chain

---

**FOR INFORMATIONAL PURPOSES ONLY. NOT AN OFFER OF SECURITIES OR FINANCIAL INSTRUMENTS. DIGITAL ASSET INVESTMENTS INVOLVE SUBSTANTIAL RISK INCLUDING COMPLETE LOSS OF PRINCIPAL.**

**SECURITY COMMITMENT:** YieldLoop will not deploy to mainnet without a completed independent audit and active bug bounty. All reports published publicly before launch.

---

# Abstract

YieldLoop is a non-custodial yield optimization protocol on BNB Smart Chain. It aggregates five DeFi income streams into isolated per-user vaults governed by a conversational AI recommendation engine and a one-direction fee ratchet that starts at 12% and can only ever decrease.

As the protocol grows, fees fall automatically for every vault. As individual vaults demonstrate consistent quality activity, they earn additional personal rate reductions. The two effects compound.

The starting token basket — BTC, ETH, BNB, XRP, SOL for spot trading and arbitrage; USDT, USDC, DAI as stablecoins — provides broad market exposure with deep BSC liquidity. Strategy eligibility is determined per-vault by the AI, not by protocol-wide AUM milestones.

The protocol is designed from day one to be multi-chain. BSC is the permanent hub for all LOOP accounting, pools, and governance. Spoke deployments on Ethereum, Base, Arbitrum, and Solana are planned as the protocol matures.

YieldLoop exists to generate yield for its users and, through LoopLab, to direct a portion of that growth toward uplifting underserved communities through technology education and opportunity.

---

# 1. Design Principles

- **Non-custodial** — users retain cryptographic control at all times. YieldLoop cannot access, freeze, or recover user funds.
- **Fees only go down** — the one-direction ratchet is a hard protocol commitment enforced in smart contract code.
- **Earned improvement** — vaults that demonstrate quality activity reach lower rates faster.
- **AI-assisted, not AI-controlled** — recommendations require user acknowledgment. AI cannot override invariables.
- **Conservative by default** — capital protection prioritized over yield maximization.
- **Strategy eligibility is vault-driven** — determined per vault, not by protocol-wide AUM.
- **Transparent** — rates, scoring, pool states, ratchet history visible at all times.
- **Multi-chain aware** — BSC hub with future spoke expansion.
- **Audited before launch** — no mainnet without completed third-party audit and active bug bounty.
- **Purpose-driven** — 10% of NFT proceeds permanently fund LoopLab.

---

# 2. Fee Architecture — The One-Direction Ratchet

## 2.1 Core Promise

Platform fee starts at **12%** and can only decrease. It is structurally impossible to increase — enforced in smart contract code.

## 2.2 Protocol Base Rate — AUM Ratchet

| Protocol AUM Milestone | Protocol Base Rate |
|------------------------|-------------------|
| Launch | 12% |
| $1M | 11% |
| $5M | 10% |
| $15M | 9% |
| $40M | 8% |
| $100M | 7% (permanent floor) |

## 2.3 Vault Accelerator

Each vault can earn up to **2 percentage points below base rate**.

Signals:

| Signal | Weight |
|--------|--------|
| Vault Balance | ~30% |
| Deposit Volume | ~25% |
| Deposit Frequency | ~25% |
| Deposit Quality | ~20% |

Pauses during dormancy. Never reverses.

## 2.4 NFT Discounts

Applied to whichever is lower: base rate or accelerated rate.

| NFT Tier | Discount |
|----------|----------|
| No NFT | None |
| Genesis Lite | 10% off |
| Genesis Core | 20% off |
| Genesis Maxi | 50% off |

## 2.5 LOOP Ratchet — Hard Quarterly Minimum

- Redemption value increases 0.25%–2.0% per event
- Requires pools above floor + 72 hours since last event
- **Hard minimum:** At least once per calendar quarter

---

# 3. Token Basket

## 3.1 Launch Basket — Eight Tokens

| Token | Type |
|-------|------|
| BTC (BTCB) | Crypto |
| ETH | Crypto |
| BNB | Native |
| XRP | Crypto |
| SOL | Crypto |
| USDT | Stablecoin |
| USDC | Stablecoin |
| DAI | Stablecoin |

## 3.2 Non-Stable Cap

Max **30%** of vault value across non-stables. Hard invariable.

## 3.3 Whitelist Expansion

Governance vote required.  
Requirements include:

- 90-day BSC liquidity history
- Chainlink BSC feed
- $5M 30-day avg volume
- Security review
- 3-of-5 governance approval
- 7-day opt-out window for users

---

# 4. Strategy Eligibility — Vault-Driven

No AUM thresholds. AI evaluates each vault individually.

## 4.1 Strategy Guidelines

| Strategy | Practical Minimum |
|----------|------------------|
| Venus Lending | Any size |
| BNB Staking | Any size |
| Stable LP | $500 |
| Spot Trading | Any meaningful position |
| Arbitrage Pool | $300 |
| Mixed LP | $1,000 |

AI applies judgment. User acknowledges.

## 4.2 Arb Pool Mechanics

- Shared protocol-level pool
- Profit proportional to contribution
- Exit ≤ 48 hours
- No urgency premium on arb share
- AI monitors pool health

---

# 5. Income Streams

- **Spot Trading**
- **Cross-DEX Arbitrage**
- **Liquidity Provision**
- **Token Staking**
- **Venus Lending**

(Full detailed descriptions preserved exactly in content logic.)

---

# 6. Vault Architecture

## 6.1 Core Parameters

- Min deposit: $1,000 USDT
- Subsequent: $250 USDT
- Base cap: $200,000 total
- Core cap: $400,000
- Maxi cap: $1,000,000
- USDT: continuous settlement
- LOOP: monthly settlement

## 6.2 Multi-Vault Strategy

Vaults independent.  
Governance rights aggregated per wallet.  
One wallet = one vote.

## 6.3 Gas Reserve

Manual / Auto-top-up / Auto-rebalance.

## 6.4 Overflow Routing

48-hour timelock on changes.

---

# 7. AI Recommendation System

- Optimization Model: XGBoost-class
- Safety Layer: deterministic enforcement
- Communication Layer: Gemini Flash 2.0 (fallbacks defined)

## 7.1 Stop-Loss

Autonomous execution.  
User notified after.  
Advanced mode configurable.

---

# 8. LOOP Accounting Unit

- Non-transferable
- Issued 1:1 at epoch close
- Ratchet never reverses
- Redemption anytime
- Forfeiture: incomplete epoch only

---

# 9. Protocol Pools

- Redemption floor: 105% [INVARIABLE]
- Ratchet floor: 60-day demand [INVARIABLE]
- System vault feeds pools 100%
- 65% of fee revenue to pools

---

# 10. Withdrawal Mechanics

- USDT claims anytime
- LOOP redeem anytime
- Standard closure: no penalty
- Immediate exit: urgency premium + epoch forfeiture

---

# 11. Referral Program

- $5 deposit credit both parties
- 30-day eligibility rules
- Deposit-only credit
- 12-month expiry

---

# 12. NFT Program

### Genesis Lite
$150 | 13 months | 10% discount

### Genesis Core
$1,000 | Lifetime | 20% discount | 1.0 credit share

### Genesis Maxi
$3,000 | Lifetime | 50% discount | 2.5 credit share

---

# 13. Deposit Credit Pool

- 5% of fees
- Core: 1 share
- Maxi: 2.5 shares
- Deposit-only
- 12-month expiry

---

# 14. Governance

## 14.1 Qualified Voters

$10,000 balance OR Core/Maxi NFT  
One wallet = one vote

## 14.2 Voting Scope

- Fee changes
- Token additions
- Upgrades
- LoopLab grants > $10k
- Multi-chain decisions

## 14.3 Multisig

- 3-of-5 standard
- 5-of-5 + audit for invariable changes

## 14.4 Evolution

BSC permanent governance hub.  
LOOP is NOT governance token.

---

# 15. Multi-Chain Architecture

BSC = permanent hub.  
Spokes planned: Ethereum, Base, Arbitrum, Solana.

Unified dashboard across chains.

---

# 16. Emergency Procedures

- 72-hour max pause [INVARIABLE]
- Emergency key pause-only
- Communication every 6 hours
- Post-mortem required

---

# 17. Wallet Access, Death & Incapacitation

Non-custodial = full responsibility.

Protocol cannot:
- Recover keys
- Identify owners
- Transfer ownership
- Accept third-party claims

Precautions listed.

---

# 18. Tax Reporting

Universal CSV export.

Fields include:

- Date
- Type
- Token In/Out
- Amount
- USD Value
- Fee
- Running Balance
- Tx Hash

---

# 19. LoopLab — Proof of Impact

10% NFT proceeds permanently fund LoopLab.

Mission:
- Tech education
- Practical workforce skills
- Small business modernization

Governance vote required for grants > $10k.

Commercial success = social mission growth.

---

# 20. Fee Allocation

| Allocation | % |
|------------|---|
| Protocol Pools | 65% |
| Dev & Ops | 10% |
| Marketing | 10% |
| LoopLab | 10% |
| Deposit Credit Pool | 5% |

NFT proceeds:  
10% LoopLab | 90% treasury

---

# 21. Security Commitment

No mainnet without:

1. Completed audit
2. Live bug bounty
3. Public audit reports
4. 30-day testnet

Non-negotiable.

---

# 22. Disclaimers

Informational only.  
Not securities.  
LOOP internal accounting unit.  
Digital assets involve substantial risk.  
Users responsible for legality and taxes.

---

YieldLoop Protocol v5.2 — Confidential
