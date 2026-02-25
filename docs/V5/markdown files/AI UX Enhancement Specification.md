# YIELDLOOP  
## AI UX Enhancement Specification  
### Seven Capability Layers for the Communication Layer | v1.0  

---

**v1.0 — Companion Document**

Additive to `YieldLoop_09_UI_UX_Specification.docx (v5.2)` and `YieldLoop_04_AI_System_Rules.docx (v5.2)`.  
No existing UI/UX rules are modified or overridden.  

All seven capability layers operate within the existing Communication Layer (Gemini Flash 2.0).  
No new AI model is required.

---

# 0. Purpose & Scope

YieldLoop’s existing UI/UX Specification establishes an unusually high baseline for a DeFi protocol: fee always visible, no dark patterns, mobile-first, risk never buried, plain language throughout. This companion document extends that foundation by specifying how the Communication Layer graduates from a translation engine into an active UX partner.

The existing Communication Layer converts system outputs — epoch closes, rebalance events, alert triggers — into readable text. That is the floor. This document defines the ceiling: seven capability layers that make YieldLoop feel less like a DeFi protocol and more like a trusted financial guide that happens to run on-chain.

### Governing principle

Every AI UX feature in this document must pass one test: does it make a first-time user feel more confident, or a returning user feel more in control? If the answer to both is no, the feature does not ship.

---

# 1. Conversational Onboarding

## 1.1 The Problem

The existing vault creation flow is a 6-step form. Forms work. But for users who have never held crypto, or who arrive from a LoopLab community context, a form is cold. It asks for inputs before explaining stakes. It assumes fluency in terminology like “risk tier,” “gas reserve,” and “overflow routing.” The result is drop-off at step 1 or, worse, uninformed decisions at steps 3–6.

---

## 1.2 The Solution: AI-Guided First Vault Flow

When a wallet connects for the first time and has no existing vaults, the AI Onboarding Guide activates as a conversational overlay on the existing `/vault/new` flow. It does not replace the form — it contextualises it. The form fields beneath remain the ground truth; the AI layer sits above as a guide.

### ONBOARDING FLOW — FIRST WALLET CONNECTION

// Triggered when: wallet connected, 0 existing vaults

AI: “Welcome to YieldLoop. Before we set anything up,
I want to understand what you’re looking for.”

AI: “Are you looking to:”
[A] Grow savings steadily, keeping most in stable assets
[B] Balance growth and stability
[C] Maximise returns, comfortable with more volatility

// User selects A → maps to Conservative risk tier
// AI explains what that means before any form field appears

AI: “Conservative means at least 70% of your vault stays in
stablecoins at all times. The AI manages the rest.
Fees start at 12% — but only on profits, never your
deposit — and the fee can only ever go down from here.”

AI: “How much were you thinking of starting with?”
[Under $1,000]  [$1,000–$10,000]  [$10,000+]  [Not sure yet]

// Maps to deposit guidance, not a gate. AI acknowledges positively.

---

## 1.3 What the AI Explains at Each Step

| Step | AI Contextualisation |
|------|----------------------|
| Risk tier selection | Plain-language description of what Conservative / Balanced / Growth actually means for their money day-to-day. No jargon. |
| Deposit amount | What the AI will do with it. What gas reserve is and why it matters. No minimum lecture — just context. |
| Gas reserve | “Gas is the transaction fee on BNB Smart Chain. Your vault keeps a small amount ready so it can act when opportunities arise. We recommend X% for a vault your size.” |
| Overflow routing | “If your vault ever hits its ceiling, your profits route here automatically. Most users choose their own wallet.” Pre-fills the connected address. |
| Backup phrase | “This is not a password reset option. If you lose this phrase, your vault is permanently inaccessible. Nobody at YieldLoop can recover it for you.” Tone: calm, not alarmist. |
| Summary before confirmation | AI reads back all choices in one plain paragraph. |

---

## 1.4 Returning User Re-entry

If a returning user creates a second vault, the full onboarding guide does not re-activate. A lighter version offers:

> “Want this vault to mirror Vault 1’s settings, or start fresh?”

---

## 1.5 Non-Negotiable Rules for Onboarding AI

- AI never recommends a specific risk tier.
- AI never frames any deposit size as too small or too large.
- Every AI statement touching fees, APY, or returns carries the **‘estimated — not guaranteed’** caveat at identical visual weight.
- AI guide can be dismissed at any step.
- Onboarding conversation is not stored beyond vault configuration.

---

# 2. Narrative Epoch Summaries

## 2.1 The Problem

Structured data cards are accurate — but not conversational.

---

## 2.2 The Solution: AI-Written Epoch Narratives

### Example Output

EPOCH 14 SUMMARY — Vault 1

AI: “Good week. Your vault earned $34.20 after fees.
BNB staking was your strongest performer this epoch,
contributing about 60% of returns. Stable LP was
steady. Spot trading sat out — conditions weren’t right.”

AI: “Your accelerator score ticked up by 1.2 points…”

AI: “The protocol crossed $820,000 AUM this epoch…”

Structured data card always remains below.

---

## 2.3 Narrative Content Rules

| Element | Rule |
|----------|------|
| Earnings | Always USDT. Never LOOP only. |
| Strategy attribution | Plain language names. |
| Accelerator | Mention only if changed. |
| Protocol milestones | Only if within 25%. |
| Tone | Warm, not exaggerated. |
| Length | Max 4 sentences above fold on mobile. |

---

## 2.4 Difficult Epoch Narratives

### Stop-Loss Example

AI: “This epoch, your vault’s stop-loss activated for BNB.
BNB fell 14%…
The AI moved that position to USDT.
You avoided approximately $47 in further loss.”

AI: “No action is required from you.”

Email always sent.

---

# 3. Proactive Contextual Guidance

## 3.1 The Problem

Reactive alerts ≠ guidance.

---

## 3.2 The Solution: AI Insight Cards

Observations, never directives.

---

## 3.3 Insight Card Triggers

| Trigger | Card Content |
|----------|--------------|
| Conservative 6+ epochs | Suggest scenario comparison |
| Vault dormant | Accelerator paused |
| Near NFT break-even | Show math |
| AUM milestone near | Ratchet progress |
| Gas low | Top-up |
| Multi-vault duplication | Compare vaults |
| Ratchet approaching | LOOP value context |

---

## 3.4 Rules

- Max 1 card per vault.
- Dismissible permanently.
- No appearance within 24h of stop-loss.
- Never directive language.

---

# 4. Intelligent Fee Estimator

## 4.1 Problem

Static numbers ≠ understanding.

---

## 4.2 Narrative Estimator Layer

### Example

AI: “At this deposit pace, you’ll contribute roughly $9,800
over 24 months…
If the protocol reaches $1M AUM…
That’s worth approximately $X to you.”

Always: **estimated — not guaranteed**

---

## 4.3 Scenario Comparison Mode

Authenticated “what if” modeling.

---

## 4.4 Rules

- Never lead with best-case.
- No guaranteed projections.
- Informational only.

---

# 5. Wallet Connection Demystification

## 5.1 The Problem

Wallet connection = biggest drop-off.

---

## 5.2 First-Time Connection Guide

AI: “Connecting your wallet is like introducing yourself…”

Non-custodial explanation shown once.

---

## 5.3 Backup Phrase Framing

AI: “You’re about to see your vault’s backup phrase.
Write them down somewhere physical.
Not a screenshot.”

2-word confirmation unchanged.

---

## 5.4 Wallet Help Hub

Plain-language explanations:

- What is a wallet?
- What can YieldLoop see?
- What happens if I lose access?
- What is non-custodial?

---

# 6. Tax Export Narration

## 6.1 The Problem

CSV ≠ clarity.

---

## 6.2 AI Tax Summary

AI: “Your export covers 14 epochs and contains 3 types
of events…”

Always hedged language. Not tax advice.

---

## 6.3 Accountant Summary PDF

One-page summary + glossary.

---

## 6.4 Rules

- Hedged language only.
- No jurisdiction-specific claims (v1.0).
- Export never gated.

---

# 7. Anomaly Explanation

## 7.1 The Problem

Correct actions can look dramatic.

---

## 7.2 Pre-Emptive Explanation

### Rebalance Example

[TEAL TOAST] “Your vault rebalanced overnight — here’s why”

AI: “During Epoch 18, BNB fell 11.2%…
The AI moved your BNB position…”

---

## 7.3 Event Taxonomy

| Event | Tone | Notification |
|--------|------|--------------|
| Stop-loss | Calm | RED toast + email |
| Routine rebalance | Neutral | TEAL |
| Safety cap | Reassuring | AMBER |
| Arb exit | Neutral | TEAL |
| Strategy unavailable | Matter-of-fact | AMBER |
| Gas depleted | Urgent | Persistent card |
| Protocol pause | Calm | RED full-width |

---

## 7.4 Rules

- No internal terminology.
- No fault framing.
- No market speculation.
- Stop-loss always delivered.

---

# 8. Implementation

## 8.1 Architecture Position

All seven layers operate inside Communication Layer (Gemini Flash 2.0).

It describes outputs only.  
It never modifies them.

---

## 8.2 Prohibited Phrases

| Prohibited | Use Instead |
|------------|------------|
| I recommend | You might consider |
| You should | One option would be |
| Guaranteed | Estimated |
| Don’t worry | Explain calmly |
| As an AI | Omit |
| Internal terms | Plain language |
| Amazing | Good epoch |

---

## 8.3 Rollout Priority

| Phase | Features |
|--------|----------|
| 1 | Anomaly + Narrative Epoch |
| 2 | Wallet + Onboarding |
| 3 | Estimator + Insight Cards |
| 4 | Tax Narration |

---

# 9. Non-Negotiable Rules — AI UX Layer

Rule 21 — No risk tier recommendation  
Rule 22 — No insight within 24h of stop-loss  
Rule 23 — Estimated — not guaranteed required  
Rule 24 — Anomaly proactive  
Rule 25 — Hedged tax language  
Rule 26 — No internal terminology  
Rule 27 — Onboarding guide dismissible  

---

# End of Document

**YieldLoop AI UX Enhancement Specification — v1.0**  
Companion to `YieldLoop_09_UI_UX_Specification.docx (v5.2)`  
All features additive. No existing rules modified or overridden.
