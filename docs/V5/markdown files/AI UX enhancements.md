# YieldLoop AI UX Enhancement Specification

> Seven Capability Layers for the Communication Layer | v1.0

**v1.0** — Companion Document. Additive to `YieldLoop_09_UI_UX_Specification.docx` (v5.2) and `YieldLoop_04_AI_System_Rules.docx` (v5.2). No existing UI/UX rules are modified or overridden. All seven capability layers operate within the existing Communication Layer (Gemini Flash 2.0). No new AI model is required.

-----

## 0. Purpose & Scope

YieldLoop’s existing UI/UX Specification establishes an unusually high baseline for a DeFi protocol: fee always visible, no dark patterns, mobile-first, risk never buried, plain language throughout. This companion document extends that foundation by specifying how the Communication Layer graduates from a translation engine into an active UX partner.

The existing Communication Layer converts system outputs — epoch closes, rebalance events, alert triggers — into readable text. That is the floor. This document defines the ceiling: seven capability layers that make YieldLoop feel less like a DeFi protocol and more like a trusted financial guide that happens to run on-chain.

> **Governing principle:** Every AI UX feature in this document must pass one test: does it make a first-time user feel more confident, or a returning user feel more in control? If the answer to both is no, the feature does not ship.

-----

## 1. Conversational Onboarding

### 1.1 The Problem

The existing vault creation flow is a 6-step form. Forms work. But for users who have never held crypto, or who arrive from a LoopLab community context, a form is cold. It asks for inputs before explaining stakes. It assumes fluency in terminology like “risk tier,” “gas reserve,” and “overflow routing.” The result is drop-off at step 1 or, worse, uninformed decisions at steps 3–6.

### 1.2 The Solution: AI-Guided First Vault Flow

When a wallet connects for the first time and has no existing vaults, the AI Onboarding Guide activates as a conversational overlay on the existing `/vault/new` flow. It does not replace the form — it contextualises it. The form fields beneath remain the ground truth; the AI layer sits above as a guide.

```
ONBOARDING FLOW — FIRST WALLET CONNECTION

// Triggered when: wallet connected, 0 existing vaults

AI: "Welcome to YieldLoop. Before we set anything up,
     I want to understand what you're looking for."

AI: "Are you looking to:"
     [A] Grow savings steadily, keeping most in stable assets
     [B] Balance growth and stability
     [C] Maximise returns, comfortable with more volatility

// User selects A → maps to Conservative risk tier
// AI explains what that means before any form field appears

AI: "Conservative means at least 70% of your vault stays in
     stablecoins at all times. The AI manages the rest.
     Fees start at 12% — but only on profits, never your
     deposit — and the fee can only ever go down from here."

AI: "How much were you thinking of starting with?"
     [Under $1,000]  [$1,000–$10,000]  [$10,000+]  [Not sure yet]

// Maps to deposit guidance, not a gate. AI acknowledges positively.
```

### 1.3 What the AI Explains at Each Step

|Step                       |AI Contextualisation                                                                                                                                                                             |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|Risk tier selection        |Plain-language description of what Conservative / Balanced / Growth actually means for their money day-to-day. No jargon.                                                                        |
|Deposit amount             |What the AI will do with it. What gas reserve is and why it matters. No minimum lecture — just context.                                                                                          |
|Gas reserve                |“Gas is the transaction fee on BNB Smart Chain. Your vault keeps a small amount ready so it can act when opportunities arise. We recommend X% for a vault your size.”                            |
|Overflow routing           |“If your vault ever hits its ceiling, your profits route here automatically. Most users choose their own wallet.” Pre-fills the connected address.                                               |
|Backup phrase              |“This is not a password reset option. If you lose this phrase, your vault is permanently inaccessible. Nobody at YieldLoop can recover it for you.” Tone: calm, not alarmist.                    |
|Summary before confirmation|AI reads back all choices in one plain paragraph. “You’re creating a Conservative vault starting with X USDT. Your gas reserve is set to Y%. Your fee starts at 12% and will only ever decrease.”|

### 1.4 Returning User Re-entry

If a returning user creates a second vault, the full onboarding guide does not re-activate. A lighter version offers: “Want this vault to mirror Vault 1’s settings, or start fresh?” The AI pre-fills matching settings and highlights differences the user may want to consider.

### 1.5 Non-Negotiable Rules for Onboarding AI

- AI never recommends a specific risk tier. It describes each and lets the user choose.
- AI never frames any deposit size as too small or too large.
- Every AI statement touching fees, APY, or returns carries the ‘estimated — not guaranteed’ caveat at identical visual weight to surrounding text.
- AI guide can be dismissed at any step with ‘Skip guide’ — the underlying form remains fully functional.
- Onboarding conversation is not stored or used to personalise future AI outputs beyond the vault settings it helped configure.

-----

## 2. Narrative Epoch Summaries

### 2.1 The Problem

The existing spec defines epoch summaries as structured data cards: balance, yield earned, fee deducted, LOOP issued. This is accurate and compliant. It is also the equivalent of handing someone a bank statement and calling it a conversation. Most users — especially those new to DeFi — will not know what to do with a table of numbers.

### 2.2 The Solution: AI-Written Epoch Narratives

At each epoch close, the Communication Layer generates a personalised narrative summary in addition to the structured data card. The data card remains the source of truth. The narrative sits above it as a human-readable interpretation.

```
EPOCH SUMMARY NOTIFICATION — EXAMPLE OUTPUT

EPOCH 14 SUMMARY — Vault 1

AI: "Good week. Your vault earned $34.20 after fees.
     BNB staking was your strongest performer this epoch,
     contributing about 60% of returns. Stable LP was
     steady. Spot trading sat out — conditions weren't right."

AI: "Your accelerator score ticked up by 1.2 points. At
     this pace, you're about 4 epochs from the next fee
     reduction."

AI: "The protocol crossed $820,000 AUM this epoch.
     You're 18% of the way to the $1M milestone that drops
     everyone's fee to 11%."

[ See Full Breakdown ]  [ Understand My Accelerator ]

// Structured data card shown below — always present
// Narrative is generated fresh each epoch, never templated
```

### 2.3 Narrative Content Rules

|Element             |Rule                                                                                                        |
|--------------------|------------------------------------------------------------------------------------------------------------|
|Earnings            |Always in USDT. Never in LOOP units. Never as a percentage without the USDT figure alongside.               |
|Strategy attribution|Name the top 1–2 strategies in plain language (BNB staking, stable LP). Never use internal allocation codes.|
|Accelerator mention |Only if score changed this epoch. If unchanged, omit.                                                       |
|Protocol milestones |Only if within 25% of next AUM milestone. Otherwise omit.                                                   |
|Market commentary   |Permitted only as context for a strategy that sat out or underperformed. Never as general market opinion.   |
|Tone                |Warm but not sycophantic. “Good week” is fine. “Amazing performance!” is not.                               |
|Length              |Maximum 4 sentences above the fold on mobile. Full version on expand.                                       |

### 2.4 Difficult Epoch Narratives

When an epoch produces negative returns or a stop-loss triggers, the AI does not default to reassurance. It states what happened, why (in plain language), and what the protocol did in response.

```
DIFFICULT EPOCH — STOP-LOSS TRIGGERED

AI: "This epoch, your vault's stop-loss activated for BNB.
     BNB fell 14% during the epoch and crossed the drawdown
     threshold for your Conservative tier. The AI moved
     that position to USDT. You avoided approximately $47
     in further loss. Your vault is now 94% stablecoin."

AI: "No action is required from you. Your next epoch will
     reassess conditions before re-entering any position."

// Email notification always sent regardless of opt-in (rule 14)
```

-----

## 3. Proactive Contextual Guidance

### 3.1 The Problem

The existing alert system is reactive: something happens, a notification fires. This is correct behaviour. But it does not constitute guidance. A user who has been Conservative for 12 months may genuinely benefit from considering Balanced — but they will never know unless someone asks. The protocol currently does not ask.

### 3.2 The Solution: AI Insight Cards

AI Insight Cards appear in the vault detail view and dashboard. They are generated by the Communication Layer based on vault behaviour patterns. They are never directives. They are observations with an invitation to explore.

> **Critical distinction:** Insight Cards suggest exploration, never action. They never say ‘switch to Balanced.’ They say ‘your vault’s pattern suggests you might be comfortable with slightly more exposure. Here’s what that would have looked like historically.’ The user then decides.

### 3.3 Insight Card Triggers

|Trigger                                                             |Card content and CTA                                                                                                                                                  |
|--------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|Conservative for 6+ epochs, consistent deposits, no stop-loss events|“Your vault has been consistently Conservative. Based on your deposit pattern, a small Balanced allocation might suit you. Here’s a comparison.” [ Compare Scenarios ]|
|Vault dormant for 2 epochs                                          |“Your vault hasn’t had a deposit in 2 epochs. Your accelerator is paused — it won’t drop, but it won’t grow until activity resumes.” [ Make a Deposit ]               |
|Near NFT break-even based on current vault data                     |“At your current vault size and rate, a Genesis Lite NFT would break even in approximately X months. Here’s the maths.” [ Show Me the Maths ]                         |
|AUM milestone within 15%                                            |“The protocol is within 15% of the $1M milestone. When it crosses, your fee drops to 11% automatically.” [ See Ratchet Progress ]                                     |
|Gas reserve below 20% of recommended                                |“Your gas reserve is running low. When it’s depleted, the AI can’t execute trades for your vault.” [ Top Up Gas ]                                                     |
|Multi-vault: near-identical settings across vaults                  |“Vaults 1 and 2 have nearly identical settings. You might want to differentiate strategies across vaults.” [ Compare Vaults ]                                         |
|Quarterly ratchet minimum approaching (75+ days)                    |“No ratchet event has fired in 75 days. If both pools remain healthy, one fires at the next epoch close. Your LOOP value will increase.” [ LOOP Details ]             |

### 3.4 Insight Card Rules

- Maximum one Insight Card visible per vault at any time. Cards do not stack.
- Cards can be permanently dismissed for that trigger type. “Don’t show me this again” is always available.
- Cards never appear during protocol pause. Emergency UI takes precedence.
- Cards never appear within 24 hours of a stop-loss or forced rebalance event.
- No card may imply the AI “recommends” a specific action. Language: “you might consider,” “based on your pattern,” “would have looked like historically.”

-----

## 4. Intelligent Fee Estimator

### 4.1 The Problem

The existing fee estimator accepts deposit amount, monthly addition, NFT tier, and timeframe, then shows a fee trajectory. This is a significant improvement over most DeFi protocols. But it is static: it shows numbers, not meaning. A user sees a table. They do not see a story about what happens to their fee if the protocol grows, or how their deposit pace affects their personal rate.

### 4.2 The Solution: Narrative Estimator Layer

The existing estimator inputs and outputs remain unchanged. The AI layer adds a plain-language narrative beneath the projection that interprets the numbers and draws out the implications.

```
FEE ESTIMATOR — AI NARRATIVE OUTPUT

// Inputs: $5,000 deposit, $200/month, no NFT, 24 months

AI: "At this deposit pace, you'll contribute roughly $9,800
     over 24 months. If the protocol reaches $1M AUM during
     that period — which at current growth pace is probable
     within 10–14 months — your fee drops from 12% to 11%
     automatically. That's worth approximately $X to you."

AI: "Your vault accelerator also builds over time. Consistent
     monthly deposits are the strongest single signal. At this
     pace, you're likely to earn a 0.5–1.0% personal rate
     reduction within 12 months — on top of any protocol drops."

AI: "These are estimates based on current conditions.
     Not guaranteed. Actual results depend on market conditions,
     protocol growth, and your vault's activity."

// 'Not guaranteed' — identical visual weight to surrounding text
// Per non-negotiable rule 15 in UI/UX Spec v5.2
```

### 4.3 Scenario Comparison Mode (Authenticated Users)

When a user has an active vault, the authenticated estimator gains a comparison mode. The AI shows the “what if” of the user’s actual vault alongside a hypothetical scenario.

```
ESTIMATOR — SCENARIO COMPARISON (AUTHENTICATED)

[ Your Current Pace ]  vs  [ Scenario: +$100/month ]

AI: "Adding $100 per month to your current pace would push
     your accelerator into the next band approximately 3
     epochs faster. That's roughly a 0.3% additional fee
     reduction worth approximately $X over 12 months."

[ NFT comparison ]

AI: "A Genesis Core NFT at your current vault size breaks
     even in approximately 21 months. If the protocol reaches
     $5M AUM before then, the break-even shortens to
     approximately 17 months."

// All projections carry 'estimated — not guaranteed' label
```

### 4.4 Estimator AI Rules

- Narrative never leads with best-case projections. Default to mid-range assumptions.
- Protocol AUM growth projections framed as ‘at current pace’ or ‘historically consistent with’ — never a commitment or guarantee.
- Accelerator estimates framed as ‘likely’ or ‘approximately’ — scoring outcomes cannot be guaranteed.
- Scenario comparison never implies the alternative is recommended. It is presented as information only.

-----

## 5. Wallet Connection Demystification

### 5.1 The Problem

Wallet connection is the single highest drop-off point for non-crypto users across all DeFi products. The anxiety has three sources: unfamiliar UI patterns, fear of making an irreversible mistake, and genuine confusion about what “non-custodial” means in practice. YieldLoop’s onboarding already includes a solid backup phrase flow. This section extends that to the connection moment itself.

### 5.2 First-Time Connection Guide

The AI detects first-time wallet connections (no prior session, no existing vault). It activates a lightweight connection guide that runs alongside the standard wallet modal. This guide does not replace any existing wallet connection mechanics.

```
WALLET CONNECTION — FIRST-TIME GUIDE

// Shown alongside standard wallet modal

AI: "Connecting your wallet is like introducing yourself.
     YieldLoop can see your public address — nothing else.
     We cannot access your funds, move assets, or see your
     other wallets or accounts."

AI: "If this is your first crypto wallet, that's fine."
     [ Help me set up a wallet ] → routes to /help/wallet

// After connection:
AI: "Connected. Your wallet address ends in ...4f2a.
     This is your identity on YieldLoop."

// Non-custodial explanation — shown once, dismissible
AI: "Important: YieldLoop is non-custodial. Your wallet's
     seed phrase controls your assets — not YieldLoop.
     If you lose it, nobody can recover your vault. Ever."
     [ I understand ] [ Remind me later ]
```

### 5.3 Backup Phrase Framing

The existing backup phrase flow requires users to type back two randomly selected words (non-negotiable rule 12). The AI adds a brief, calm explanation before the phrase is displayed.

```
BACKUP PHRASE — AI FRAMING (BEFORE REVEAL)

AI: "You're about to see your vault's backup phrase.
     This is 12 words. Write them down somewhere physical.
     Not a screenshot. Not a notes app.
     If your device is lost, stolen, or wiped, this phrase
     is the only way to restore access to your vault.
     YieldLoop cannot restore it for you. Nobody can."

AI: "Ready? This is the one time we'll show it automatically."
     [ Show My Phrase ]

// Existing phrase reveal and 2-word confirmation follows unchanged
```

### 5.4 Wallet Help Hub (`/help/wallet`)

|Topic                         |Plain-language target                                                                                                            |
|------------------------------|---------------------------------------------------------------------------------------------------------------------------------|
|What is a wallet?             |Not a bank account. A key pair. Your address is public; your private key is not. MetaMask and Trust Wallet explained as examples.|
|What can YieldLoop see?       |Your public address and on-chain activity related to your YieldLoop vaults. Nothing in other wallets or accounts.                |
|What happens if I lose access?|Your backup phrase is the only recovery path. YieldLoop cannot help. Honest, calm, not alarmist.                                 |
|What is non-custodial?        |You hold the keys. YieldLoop holds nothing on your behalf. Comparison: a safety deposit box where only you have the key.         |

-----

## 6. Tax Export Narration

### 6.1 The Problem

Tax export is present and available without restriction (non-negotiable rule 20). The output is accurate. It is not, however, usable by most people without accounting help. A CSV of taxable events is raw data. For users new to crypto taxation, the export is a starting point, not an answer.

### 6.2 The Solution: AI Tax Summary

When a user generates a tax export, the AI adds a plain-language summary above the download link. This summary is not tax advice. It identifies categories of events, explains what each type likely represents, and tells the user what to give their accountant.

```
TAX EXPORT — AI NARRATIVE HEADER

TAX EXPORT — 2025 Financial Year

AI: "Your export covers 14 epochs and contains 3 types
     of events:"

     Yield income: 14 events totalling $892.40 USDT
     Likely treated as ordinary income in most jurisdictions,
     earned at the time of each epoch close.

     Asset disposals: 6 events (spot trading exits)
     May generate capital gains or losses depending on your
     jurisdiction and holding period.

     LOOP redemptions: 2 events totalling $124.80 USDT
     Treatment varies. Likely income at time of redemption.

AI: "The CSV below contains all event details your accountant
     will need: timestamps, amounts in USDT, transaction hashes.
     This is not tax advice."

[ Download CSV ]  [ Download Accountant Summary PDF ]
```

### 6.3 Accountant Summary PDF

A secondary export option generates a one-page Accountant Summary PDF: the AI narrative above, a plain-language glossary of YieldLoop-specific terms (epoch, LOOP, accelerator), and a summary table of event totals by category. Transaction hashes remain in the CSV only.

### 6.4 Tax Narration Rules

- AI uses hedged language throughout: “likely treated as,” “may generate,” “treatment varies.” Never declarative tax claims.
- No jurisdiction-specific claims in v1.0. Future: user-set jurisdiction in account settings.
- “This is not tax advice” appears on every AI tax output in the same visual weight as surrounding text.
- Tax export always available, never gated, no minimum period (non-negotiable rule 20).

-----

## 7. Anomaly Explanation

### 7.1 The Problem

The Safety Layer vetoes recommendations that violate invariables and logs anomalies. The Optimization Model can produce outputs that, when executed, look dramatic to users: a sudden 8% rebalance out of BNB staking, a stop-loss triggering overnight, an arb pool exit appearing as a separate settlement line. These are correct behaviours. But users who see them without context may panic or take manual actions that undermine the strategy.

### 7.2 The Solution: Pre-Emptive Explanation

When the Execution Layer completes any non-routine action — stop-loss, forced rebalance, arb exit, Safety Layer veto — the Communication Layer generates an explanation and delivers it before the user notices the change in their dashboard. The explanation arrives as a toast notification that expands to full context.

```
ANOMALY EXPLANATION — REBALANCE EVENT

// Toast — appears on next dashboard load or as push notification

[TEAL TOAST] "Your vault rebalanced overnight — here's why"

// On expand:
AI: "During Epoch 18, BNB fell 11.2% against a 10% drawdown
     threshold set for your Conservative tier. The AI moved
     your BNB position (4.8% of vault) to USDT automatically.
     This is expected behaviour for your risk tier."

AI: "Your vault is now 97% stablecoin. The next epoch will
     reassess conditions."

AI: "No action is required from you."

[ See Transaction ]  [ Understand Stop-Loss ]  [ Dismiss ]
```

```
ANOMALY EXPLANATION — SAFETY LAYER CAP APPLIED

// Rare event — AI recommendation adjusted by Safety Layer

[AMBER TOAST] "This epoch's allocation was adjusted"

// On expand:
AI: "The AI recommended allocating 32% of your vault to
     non-stable assets this epoch. The protocol's hard limit
     is 30%. The Safety Layer automatically adjusted this
     to 30% before execution. Your vault is operating normally."

AI: "This is the system working as designed."

[ See Full Allocation ]  [ Dismiss ]
```

### 7.3 Anomaly Event Taxonomy

|Event Type                        |Trigger Timing               |Tone                                  |Notification                                    |
|----------------------------------|-----------------------------|--------------------------------------|------------------------------------------------|
|Stop-loss execution               |Immediate — next load + email|Calm, factual, no action required     |RED toast + email (always, rule 14)             |
|Routine rebalance (>5% shift)     |Next dashboard load          |Neutral, informative                  |TEAL toast                                      |
|Safety Layer cap applied          |Next dashboard load          |Reassuring: system working as designed|AMBER toast                                     |
|Arb pool exit                     |Settlement epoch             |Neutral, explains settlement          |TEAL card in activity                           |
|Strategy unavailable (pool health)|Next epoch close             |Matter-of-fact, temporary             |AMBER toast                                     |
|Gas reserve depleted mid-epoch    |Real-time                    |Urgent but not alarming               |AMBER persistent card                           |
|Protocol pause                    |Immediate                    |Calm: your funds are safe             |RED full-width banner, not dismissible (rule 13)|

### 7.4 Anomaly Explanation Rules

- Explanations never use internal system terminology (veto, anomaly, Safety Layer rejection, XGBoost). Plain language only.
- Explanations never imply fault — events are presented as designed behaviour.
- Explanations never speculate about future strategy. “The next epoch will reassess” is acceptable. “BNB should recover” is not.
- Stop-loss explanation is always delivered regardless of email opt-in, matching non-negotiable rule 14.

-----

## 8. Implementation

### 8.1 Architecture Position

All seven capability layers operate within the existing Communication Layer (Gemini Flash 2.0). No new AI model is required. The Optimization Model and Safety Layer are not modified. The Communication Layer’s role expands from translation engine to UX partner.

> **Architectural rule:** The Communication Layer receives structured outputs from the Execution and Safety Layers. It produces human-readable text. It has no authority to modify, delay, or reinterpret those outputs — only to describe them. This remains true for all seven layers in this document.

### 8.2 Prohibited Phrases (All AI Outputs)

|Prohibited              |Use instead                                                                                              |
|------------------------|---------------------------------------------------------------------------------------------------------|
|“I recommend…”          |“You might consider,” “based on your pattern”                                                            |
|“You should…”           |“One option would be,” “some users in this situation choose to”                                          |
|“Guaranteed” / “will”   |“Estimated,” “at current pace,” “likely”                                                                 |
|“Don’t worry”           |Acknowledge the event, explain it, state what was done.                                                  |
|“As an AI…”             |Omit entirely. The AI is part of the protocol.                                                           |
|Any internal system term|Veto, anomaly flag, Safety Layer rejection, XGBoost, allocation tensor — none appear in user-facing text.|
|“Amazing” / “incredible”|“Good epoch” is the ceiling for positive framing.                                                        |

### 8.3 Rollout Priority

|Phase|Features                                                             |Dependency                                          |Target                  |
|-----|---------------------------------------------------------------------|----------------------------------------------------|------------------------|
|1    |Anomaly Explanation (Sec. 7) + Narrative Epoch Summaries (Sec. 2)    |Execution Layer event hooks already defined in v5.2 |Launch alongside v5.2 UI|
|2    |Wallet Connection Guide (Sec. 5) + Conversational Onboarding (Sec. 1)|First-time user detection, /help/wallet page        |Month 1 post-launch     |
|3    |Intelligent Estimator Narration (Sec. 4) + AI Insight Cards (Sec. 3) |Estimator already built. Insight card trigger logic.|Months 2–3              |
|4    |Tax Export Narration + Accountant PDF (Sec. 6)                       |Tax export categorisation + PDF generation          |Months 3–6              |

-----

## 9. Non-Negotiable Rules — AI UX Layer

The following rules are additive to the 20 non-negotiable rules in `YieldLoop_09_UI_UX_Specification.docx`. They cannot be overridden by any team decision.

> **Rule 21** — AI Onboarding Guide never recommends a risk tier. It describes. The user chooses.

> **Rule 22** — AI Insight Cards cannot appear within 24 hours of a stop-loss or forced rebalance event.

> **Rule 23** — All AI projections in the estimator and narrative summaries carry the ‘estimated — not guaranteed’ caveat at identical visual weight to surrounding text.

> **Rule 24** — Anomaly explanations are delivered proactively — before the user navigates to the affected screen. Not on-demand only.

> **Rule 25** — Tax narration uses hedged language throughout: ‘likely treated as,’ ‘may generate,’ ‘treatment varies.’ Never declarative tax claims.

> **Rule 26** — No AI output may use internal system terminology visible to users. Plain language only. Veto, anomaly, Safety Layer rejection, XGBoost — none of these appear in user-facing text.

> **Rule 27** — The AI Onboarding Guide can be dismissed at any step. The underlying 6-step vault creation form must remain fully functional without the guide.

-----

*YieldLoop AI UX Enhancement Specification — v1.0*
*Companion to `YieldLoop_09_UI_UX_Specification.docx` (v5.2)*
*All features additive. No existing rules modified or overridden.*
