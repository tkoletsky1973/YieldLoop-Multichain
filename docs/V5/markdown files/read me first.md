YieldLoop — Read Me First  |  v5.2

# YIELDLOOP

## Read Me First

### Document Index, v5.2 Changelog & Navigation Guide

**Start here.** This document tells you what each file contains and what changed in v5.2.

---

# The Document Suite

| Document | What It Contains |
|---|---|
| **01 — Read Me First (this doc)** | Index, changelog, navigation |
| **02 — Whitepaper** | Protocol design, token basket, fee architecture, LoopLab, multi-chain vision |
| **03 — Appendixes** | All formulas, parameters, token addresses, oracles, break-even tables, glossary |
| **04 — AI System & Rules Engine** | Strategy eligibility, stop-loss, accelerator, authority matrix, failure modes |
| **05 — Invariables** | Every hard-coded rule — what it is, why it exists, how to change it |
| **06 — Compliance & Legal** | Disclaimers, sanctions, wallet access loss, death, incapacitation, tax export |
| **07 — Kitchen Table Talk** | Complete plain-language guide — normie journey, LOOP, fees, multi-vault, LoopLab |
| **08 — Developer & Integrator Guide** | Contracts, arb pool, governance, emergency playbook, deployment sequence |
| **09 — UI/UX Specification** | All screens, flows, estimator tool, epoch notifications, emergency UI |
| **10 — Plain English Guide** | One-page summary for sharing — no jargon, no code |

---

# v5.2 Changelog

## Strategy Eligibility — AUM Gates Removed

AUM-based strategy unlock tiers removed entirely. Strategy eligibility is now vault-driven. The AI determines what strategies suit each vault based on its size, risk tier, and pool health. Spot trading is available from day one at any vault size. Arbitrage is gated by whether the vault's contribution size makes it worthwhile — not by total protocol AUM.

## LOOP Forfeiture — Clarified

Fully issued past-epoch LOOP is always safe and never forfeited under any circumstances. Current incomplete epoch LOOP is forfeited on immediate exit. Dollar value shown before any exit is confirmed.

## Stop-Loss — Autonomous Execution

Stop-loss executes immediately when triggered. User notified after. No notify-first window — that defeats the purpose in fast markets. Users set their own thresholds in Advanced Mode.

## LOOP Ratchet — Hard Quarterly Minimum

Hard commitment: ratchet fires at least once per calendar quarter when both pools are above floor levels. Not discretionary — if 90 days pass and both pools are healthy, a ratchet fires at the next epoch close.

## Referral Program — Full Mechanics

Every vault holder gets a referral code automatically. New user applies code at signup. Both receive a $5 deposit credit exactly one calendar month after the new user's first deposit. New wallet must have no prior YieldLoop history. Referee must not have closed their vault in the 30-day window. One referral code per wallet.

## Tax Export

Universal CSV export from transaction history. All taxable events included: deposits, withdrawals, USDT claims, LOOP issuance and redemption, fees, credits, NFT purchases. Format: date, type, token in, amount in, token out, amount out, USD value at time, fee paid, running balance. No tax advice — clean data for any accountant or software.

## Wallet Loss, Death & Incapacitation

Dedicated coverage in Compliance, Kitchen Table Talk, and vault creation acknowledgment. Protocol cannot recover wallets, designate beneficiaries, or act on third-party claims. Mandatory acknowledgment checkbox at vault creation. Recommended precautions documented.

## Genesis Lite — Clock Always Runs

Once applied to a vault, the 13-month clock runs continuously. Dormancy does not pause it. Invariable — no exceptions.

## Emergency Rules — Full Playbook

Complete emergency procedures in Doc 05 (Invariables) and Doc 08 (Developer Guide). Who triggers a pause, single key scope vs multisig scope, what happens to open positions, LP and staking during pause, communication requirements, resolution process.

## Multi-Vault Strategy — Fully Documented

Multi-vault usage explicitly documented as intentional and supported. Use cases: different risk tiers, NFT optimization, overflow routing, strategy diversification. Governance rights calculated per-wallet across all vaults combined.

## Governance Rights

Qualified voter: $10,000 total balance across all vaults OR holds Genesis Core or Maxi NFT. One wallet = one vote. Voting scope: fee parameter changes, whitelist additions, protocol upgrades, LoopLab grant allocations above $10,000.

## Multi-Chain Architecture

BSC is permanent protocol hub — all LOOP, pools, and governance stay on BSC. Protocol designed multi-chain-aware from day one. Planned spokes: Ethereum, Base, Arbitrum, Solana. No timeline committed — architecture supports expansion without a rebuild.

## Fee Estimator Tool

Public tool on landing page and dashboard. Inputs: deposit amount, monthly add-on, frequency, NFT tier, timeframe. Output: fee trajectory, estimated profit range, accelerator timeline, NFT break-even. All figures show 'estimated — not guaranteed' caveat.

## Epoch Notifications

Dashboard: always on, no opt-out. Email: opt-in at signup and settings. Summary: USDT settled, LOOP issued, accelerator change, credit pool received, AUM progress, next fee milestone distance.

## LoopLab — Full Vision

Elevated from fee line item to dedicated Whitepaper section. Mission, Executive Director role, Proof of Impact framework, and protocol connection fully described. 10% of NFT proceeds fund LoopLab permanently.

## Arb Pool Mechanics — Fully Defined

Profit distribution proportional to vault contribution share. Exit: queued, paid out as positions close, max 48 hours. On urgent vault exit: arb share settles separately within 48 hours, no urgency premium applied to it.

---

# Navigation Guide

New to the protocol? Doc 10 then Doc 07.  
Investor or partner due diligence? Doc 02 then Doc 03.  
Building or integrating? Doc 08 then Doc 04.  
Legal or compliance review? Doc 06.  
Designing the frontend? Doc 09.  
Checking what cannot change? Doc 05.

---

YieldLoop Protocol v5.2 — All documents current. Verify you have the full suite before referencing externally.

**YieldLoop Protocol v5.2 — Confidential**
