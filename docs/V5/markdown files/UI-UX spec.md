# YieldLoop UI/UX Specification

> Complete Interface Design, All Screens & Flows, Fee Estimator, Epoch Notifications, Emergency UI, Multi-Vault, Tax Export | v5.2

**v5.2 changes:** AUM strategy gates removed from all UI. Stop-loss notification added. Arb pool exit shown as separate settlement line. LOOP forfeiture screen updated — past-epoch always safe. Fee estimator tool added. Epoch summary notifications defined. Multi-vault UI documented. Tax export screen added. Emergency UI added.

-----

## 1. Design System

### 1.1 Core Principles

- Fee always visible — protocol base, vault accelerated rate, and effective rate shown per-vault at all times
- Rate trajectory prominent — show where the fee is going, not just where it is now
- Estimator accessible — fee estimator on landing page and dashboard before any commitment
- No strategy gates in UI — strategy availability shown based on vault size, not AUM
- Clarity over cleverness — every screen understandable without reading documentation
- Risk always visible — never buried, never behind a scroll
- No dark patterns — no pre-ticked boxes, no hidden costs, no misleading confirmation flows
- Mobile-first — all flows designed for 375px viewport before desktop
- LOOP forfeiture always transparent — past-epoch safe status shown prominently
- Emergency states — clear degraded UI during pause, no silent failures

### 1.2 Visual Language

|Element                                 |Color / Treatment                             |
|----------------------------------------|----------------------------------------------|
|Primary brand                           |`#0A2540` — navy                              |
|Accent / interactive                    |`#00C2A8` — teal                              |
|Fee drop events                         |`#16A34A` — green — celebrated, never alarming|
|Warning states                          |`#F59E0B` — amber                             |
|Danger / cost / loss                    |`#DC2626` — red                               |
|Safe / protected                        |`#16A34A` — green                             |
|Neutral info                            |`#F5F5F5` background, `#999999` border        |
|Typography — UI                         |Inter                                         |
|Typography — numbers, addresses, amounts|JetBrains Mono                                |
|Protocol paused banner                  |`#DC2626` full-width — cannot be dismissed    |

-----

## 2. Site Architecture

**PUBLIC** (no wallet required):

```
/                    Landing page + fee estimator
/how-it-works        Protocol explainer
/fee-tracker         Live public fee tracker
/estimator           Standalone fee estimator tool
/nft                 Genesis NFT mint page
/looplab             LoopLab mission and impact
/protocol-stats      Live protocol health
/security            Audit reports, bug bounty
```

**AUTHENTICATED:**

```
/dashboard                Main dashboard — all vaults
/vault/new                Create vault — 6-step flow
/vault/[id]               Vault detail
  /vault/[id]/overview    Balance, fee, accelerator
  /vault/[id]/tokens      Token allocation, 30% cap display
  /vault/[id]/loop        LOOP portfolio and redemption
  /vault/[id]/activity    Transaction history
  /vault/[id]/settings    Gas, overflow, profit distribution
  /vault/[id]/close       Closure flow
/loop                     LOOP portfolio — all vaults combined
/history                  Full transaction history + tax export
/referral                 Referral code and earnings
/nft/my                   User NFT holdings
/buy-usdt                 Transak fiat onramp
/estimator                Authenticated estimator (uses real vault data)
/governance               Eligibility status and active votes
```

**ADMIN:**

```
/admin/overview           Protocol health
/admin/aum-tracker        AUM vs ratchet milestones
/admin/tokens             Whitelist management
/admin/accelerators       All vault accelerator scores
/admin/pools              Pool management
/admin/fees               Fee distribution
/admin/emergency          Emergency controls + pause status
/admin/looplab            LoopLab governance votes and grants
```

-----

## 3. Landing Page

```
ABOVE THE FOLD — MOBILE:
YIELDLOOP                        [ Connect Wallet ]
'Your money. Working harder.'
'Fee starts at 12% — only going down.'
[ Start Earning ]    [ How It Works ]
────────────────────────────────────────
Protocol fee today:  12.0%
Next drop to 11%:    [████████░░░░] 61% to $1M
────────────────────────────────────────
──── FEE ESTIMATOR (collapsed) ────
[ Try the Fee Estimator ↓ ]
  Deposit: [$____] Monthly add: [$____]
  NFT tier: [None ▾]  Timeframe: [24mo ▾]
  [ Show My Fee Trajectory ]
────────────────────────────────────────
SCROLL:
  How it works — 3 plain-language cards
  The fee ratchet visualised
  8 tokens shown simply
  Multi-vault intro
  LoopLab section
  NFT teaser
  Security commitment
  [ Start Earning ]
```

-----

## 4. Fee Estimator Tool

> The estimator is available pre-login on the landing page and `/estimator`, and post-login with real vault data on the dashboard. It is a trust-building tool — show users their fee path before they commit to anything.

```
FEE ESTIMATOR — FULL SCREEN:

INPUTS:
  Initial deposit:         [ $5,000      ]
  Monthly top-up:          [ $500        ]
  Deposit frequency:       [ Monthly ▾   ]
  NFT tier:                [ None ▾      ]  → [ Lite ] [ Core ] [ Maxi ]
  Planning timeframe:      [ 24 months ▾ ]

[ Calculate My Fee Path ]

OUTPUT:
─────────────────────────────────────────────
YOUR ESTIMATED FEE TRAJECTORY
Month     Protocol    Vault      Effective   Monthly saving
          Base        Rate       Rate        vs 12% baseline
  1       12.0%       12.0%      12.0%       —
  3       12.0%       11.7%      11.7%       $0.90/mo
  6       11.0%       10.6%      10.6%       $4.20/mo  ← AUM drop
  12      10.0%        9.2%       9.2%       $8.50/mo  ← AUM drop
  18       9.0%        7.4%       7.4%       $13.30/mo ← AUM drop
  24       9.0%        7.0%       7.0%       $15.00/mo

ADD GENESIS CORE NFT AT MONTH 6:
  Month 6+: effective rate → 8.5% (20% off 10.6%)
  Month 12: effective rate → 7.4% (20% off 9.2%)
  Core break-even: ~14 months at current profit estimate
  Estimated annual profit range (Conservative): $180–$420
  Estimated annual profit range (Balanced):     $280–$680
─────────────────────────────────────────────
⚠️  All figures estimated — not guaranteed.
    AUM growth timeline depends on protocol adoption.
    Profit ranges based on historical DeFi yield data.
    Past performance does not guarantee future results.
```

> **MANDATORY:** ‘Estimated — not guaranteed’ must appear in the same visual weight as the projected numbers. This is non-negotiable. The estimator is a planning tool, not a promise.

-----

## 5. Wallet Creation Flow

```
CONNECT WALLET MODAL:
'Connect your wallet to get started'
[ MetaMask ]         — shown if detected
[ Trust Wallet ]     — shown if detected
[ WalletConnect ]    — any mobile wallet
[ Create New Wallet ]  'New to crypto? Start here.'

─────────────────────────

CREATE NEW WALLET (Privy):

Step 1: Sign in
  [ Continue with Google ]
  [ Continue with Apple  ]
  [ Use Email Instead    ]
  email: [_______________]

Step 2: Verify
  'Check your phone'
  '6-digit code sent to [phone]'
  [_ _ _ _ _ _]

Step 3: Backup phrase
  ⚠️  SAVE THESE 12 WORDS
  [word1] [word2] [word3] [word4]
  [word5] [word6] [word7] [word8]
  [word9] [word10] [word11] [word12]
  [ Show Words ]  [ Hide Words ]
  [ I've Written Them Down → ]

Step 4: Confirm backup
  'Type word #4 from your phrase:'
  [___________]
  'Type word #9 from your phrase:'
  [___________]
  [ Confirm and Continue ]

Step 5: Wallet ready
  ✓ Wallet created: 0x7f3a...b21c
  [ Continue to Dashboard ]
```

> **INVARIABLE UI:** Step 4 word confirmation is mandatory. Words are randomly selected per session. User cannot proceed without typing 2 correct words. This cannot be skipped, removed, or made optional.

-----

## 6. Dashboard — Main View

```
YIELDLOOP    ● Connected: 0x7f3a    [ + Add Funds ]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PROTOCOL FEE
  Today: 12.0%
  AUM: $687K
  [████████░░░░░░] 69% to next drop (11% at $1M)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PORTFOLIO SUMMARY
  Total Balance:    $6,440
  USDT Ready: $42.30
  Total LOOP:       312 LOOP @ $1.008 = $314.50
  This Month:       +$34.20 profit

MY VAULTS
──────────────────────────────────────────────────────────
Vault 1  Conservative  $5,212  12.0% → 11.53% eff  ● Active  ›
Vault 2  Balanced      $1,228  12.0% → 11.91% eff  ● Active  ›
──────────────────────────────────────────────────────────
[ + Create New Vault ]

GOVERNANCE STATUS
  Combined balance: $6,440  ← Qualifies at $10,000
  $3,560 more needed for governance eligibility
  OR: get a Genesis Core or Maxi NFT

PROTOCOL HEALTH
  Redemption pool: 138% ● Healthy
  Ratchet pool:    94d  ● Healthy
  Last ratchet:    28 days ago
  Next quarterly minimum: 62 days
```

-----

## 7. Vault Detail — Overview Tab

```
VAULT 1  Conservative  ● Active
$5,212.40 total  |  $23.80 USDT ready

FEE STATUS
  Protocol base:   12.0%   (AUM $687K)
  Your vault:      11.53%  (0.47pp accelerator earned)
  Effective rate:  11.53%  (no NFT applied)
  ← This is what you actually pay on profits →

FEE TRAJECTORY
  Protocol drops at $1M AUM:   your rate → 10.53%
  With 2pp full accel at $5M:  your rate → 8.00%
  With Core NFT today:         your rate → 9.22%
  Best case (Maxi, $100M AUM): your rate → 2.5%
  ⚠️  All projected — AUM timeline not guaranteed

ACCELERATOR
  Earned: 0.47pp of 2.00pp max
  [████░░░░░░░░░░░░░░░░] 24%
  Signals:
  Balance:    ████░░  62%  '$5,212 — good foundation'
  Volume:     █████░  71%  '$2,200 deposited in 6mo'
  Frequency:  ████░░  65%  '4 deposits in 6mo — keep going'
  Quality:    █████░  74%  'Consistent, net positive ✓'
  Tip: 'One more deposit this month pushes
        frequency score to 80%+'
```

-----

## 8. Vault Detail — Tokens Tab

```
VAULT 1 — TOKEN ALLOCATION

NON-STABLE POSITIONS (max 30%)
  BTC     $312.80   6.0%
  ETH     $260.60   5.0%
  BNB     $364.80   7.0%
  XRP     $130.30   2.5%  via PancakeSwap ↗
  SOL     $130.30   2.5%  via BiSwap ↗
  ─────────────────────────
  Total   $1,198.80  23.0%  of 30.0% max ✓
  [████████████████░░░░░░░░░░░░░░] 23% of 30% cap

STABLE POSITIONS (77%)
  stkBNB staking   $1,563.70   30.0%
  USDT Venus        $885.40    17.0%
  USDC Venus        $416.90     8.0%
  DAI Venus         $312.80     6.0%
  USDT-BNB LP       $416.90     8.0%
  DAI-USDT LP       $312.80     6.0%
  Arb pool          $104.30     2.0%  (pending exit: none)

GAS RESERVE
  $104.30  2.0%  ● Healthy

PERFORMANCE THIS EPOCH
  BTC: +2.1%  ETH: +1.4%  BNB: +3.8%
  XRP: +4.2%  SOL: -1.2%
  SOL stop-loss not triggered (threshold: $108)
```

-----

## 9. Vault Creation — 6-Step Flow

### Step 1 — Deposit Amount

```
'How much are you depositing?'
[    1,200    ] USDT
Your wallet balance: 1,847.20 USDT
Minimum: $1,000
Gas reserve: ~$18.50 in BNB set aside automatically
This covers transaction costs — no manual gas needed
[ Next → ]
```

### Step 2 — Gas Reserve Mode

```
'How should gas be managed?'
( ) Manual — I'll top up when reminded
(•) Auto-Rebalance ← Recommended
    Uses 2% of profits. Tops up when low,
    converts excess back to USDT.
    Percentage: [ 2 ]%  (1–10%)
( ) Auto-Top-Up — converts profits to BNB only
[ ← Back ]  [ Next → ]
```

### Step 3 — Profit Distribution

```
'How would you like to receive profits?'
( ) USDT only — claim any time as trades close
(•) 50 / 50 — half USDT, half LOOP
( ) LOOP only — monthly appreciating credits
( ) Auto-Optimize — AI decides each month

Compounding:
( ) Reinvest profits back into vault
(•) Pay out — keep profits available to claim
( ) 50/50 split
[ ← Back ]  [ Next → ]
```

### Step 4 — Overflow Routing

```
'What happens when your vault reaches its ceiling?'
Your ceiling: $200,000 (standard)
Raise to $400K or $1M with a Genesis NFT

( ) Send to another wallet: [ address _________ ]
( ) Create a second vault automatically
( ) Top up an existing vault (if you have one)
( ) I'll set this up later

Note: changing this takes 48 hours to activate
[ ← Back ]  [ Next → ]
```

### Step 5 — AI Conversation

```
'Let's set your strategy.'
[Full chat interface]

AI asks goals → recommends tier → shows dollar examples
Includes worst case, fee trajectory, token exposure
User can ask any question

User approves recommendations: 'This looks good'
AI populates STRATEGY SCREEN with recommended settings
(AI controls can be overridden by the user before final authorization):
  Risk tier: Conservative
  Strategy: 48% staking / 22% LP / 18% Venus / 10% spot / 2% arb
  Stop-loss: AI-managed (can customize in Advanced Mode)
  Current fee: 12.0% on profits only
  Fee path: drops with AUM growth + vault earns accelerator

[ Adjust Settings ]  [ Strategy Looks Correct — Authorize and Go to Acknowledgment ]
[ ← Back ]
```

### Step 6 — Acknowledgment

```
'Before we activate your vault:'

[  ] I have reviewed all settings and understand them
[  ] I understand the fee starts at 12% on profits only
     and can only ever decrease — never increase
[  ] I understand fee decreases depend on protocol growth
     — not guaranteed on any specific timeline
[  ] I understand the risks and may lose all of my deposit
[  ] I understand the AI made recommendations — I accept
     sole responsibility for the settings I am activating
[  ] I understand USDT profits are claimable any time
[  ] I understand LOOP has no value outside YieldLoop
[  ] I understand YieldLoop cannot recover my wallet if I
     lose access — including through death or incapacitation.
     I accept full responsibility for my wallet security.
[  ] I confirm use of this protocol is legal in my country
[  ] I confirm I am not subject to international sanctions

'Type exactly to activate:'  I UNDERSTAND AND ACCEPT RESPONSIBILITY
[________________________________________]
(Paste disabled. Autocomplete off.)

[ ← Back ]    [ Activate Vault ] ← disabled until all 10 boxes checked
                                    AND phrase typed correctly
```

> **INVARIABLE UI RULES — ACKNOWLEDGMENT:**
> 
> - All 10 checkboxes rendered individually — no ‘select all’ option
> - No checkbox pre-ticked under any circumstances
> - Confirmation input: paste disabled, autocomplete=‘off’
> - ‘Activate Vault’ button disabled until all 10 ticked AND phrase exact
> - Vault creation cannot proceed without on-chain acknowledgment hash
> - Wallet recovery/death checkbox (box 8) cannot be removed or reworded

-----

## 10. LOOP Portfolio Screen

```
MY LOOP PORTFOLIO
Total LOOP:      312.40 LOOP
Current value:   $314.90  ($1.008 per LOOP)
Latest ratchet:  +0.75% — 28 days ago
Next quarterly:  62 days remaining (hard minimum)

EPOCH BREAKDOWN:
  Epoch 1  50.00 LOOP  issued $1.000  now $1.008  value $50.40
  Epoch 2  48.20 LOOP  issued $1.000  now $1.008  value $48.59
  Epoch 3  52.10 LOOP  issued $1.000  now $1.008  value $52.52
  Epoch 4  14.20 LOOP  current epoch  — not yet issued
            └── This LOOP would be forfeited on immediate exit

[ Redeem Selected to USDT ]    [ Redeem All to USDT ]

REDEMPTION PREVIEW:
  310.30 LOOP × $1.008 = $312.78 USDT
  Arriving in vault available balance immediately
  (Epoch 4 LOOP excluded — not yet issued)
```

-----

## 11. Withdrawal — Standard Closure

```
CLOSE VAULT 1
'How would you like to close?'
(•) Standard closure — end of month  ← Recommended
    No penalty. Clean wind-down.
    Estimated completion: 5–7 days.
( ) Immediate exit
    Costs friction + urgency fee. Some LOOP forfeited.
    [ See full cost breakdown ]

STANDARD CLOSURE TIMELINE:
  Today:              Spot positions begin closing
  24–48 hours:        LP positions exit
  Immediately:        Venus lending redeemed
  Up to 7 days:       Staking unbond completes
  Month-end:          LOOP issued for this epoch
                      All USDT available to claim

[ Cancel ]    [ Confirm Standard Closure ]
```

-----

## 12. Withdrawal — Immediate Exit

```
IMMEDIATE EXIT — FULL COST BREAKDOWN

Vault balance:                    $5,212.40

Friction costs:
  Gas fees:                            $8.20
  DEX exit fees:                      $18.40
  LP pool exit:                       $14.60
  Slippage estimate:                  $10.80
  Staking unbonding penalty:           $8.40
  Total friction:                     $60.40

Urgency premium (20% of friction):  $12.08

Arb pool portion:
  $104.30 arb allocation settles
  separately within 48 hours.
  No additional fees on this portion.

You receive now (approx):        $5,035.92
Arb settlement within 48h:         $104.30
Total received:                  $5,140.22

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
LOOP STATUS:
  ✅ SAFE — Epochs 1–3: 310.30 LOOP ($312.78)  ← always protected
  ❌ FORFEITED — Epoch 4 (current): 14.20 LOOP = $14.20
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Total cost vs waiting for month end: ~$86.68

[ ← Go Back — Wait for Month End ]   [ I Understand — Exit Now ]
```

> **MANDATORY UI RULES — IMMEDIATE EXIT:**
> 
> - ‘Go Back’ must be same or larger visual weight than ‘Exit Now’
> - Past-epoch LOOP shown in GREEN with ✅ SAFE label
> - Current-epoch LOOP shown in RED with ❌ FORFEITED label
> - Arb settlement shown as separate line — no premium applied
> - ‘Total cost vs waiting’ shown as summary dollar figure
> - All amounts shown in dollars — not just percentages

-----

## 13. Stop-Loss Notification

```
PUSH NOTIFICATION + EMAIL (always — regardless of email opt-in):

🔴 Stop-loss executed — SOL position
  Token sold:          SOL (BEP-20)
  Quantity:            18.40 SOL
  Execution price:     $108.40
  Stop-loss threshold: $108.00
  USDT received:       $1,994.56
  Time:                2025-04-14 11:23:07 UTC
  Transaction:         0x4f2a...

  'SOL dropped 23.7% from your entry price of $142.00'
  Your vault balance after: $5,380.20 USDT
  'AI will reassess SOL allocation at next rebalance.'

[ View Vault ]  [ View Transaction ]
```

> Stop-loss notifications are always sent regardless of email opt-in setting. This is a significant financial event. Users must be notified even if they have not opted into general email notifications.

-----

## 14. Epoch Summary Notification

### 14.1 Dashboard — Always On

```
EPOCH SUMMARY — APRIL 2025
(appears at top of dashboard at epoch close)

YOUR VAULTS THIS MONTH:

Vault 1  Conservative:
  USDT settled:       $23.80  [ Claim ]
  LOOP issued:        14.20 LOOP @ $1.000
  Fee paid:            $2.75  (on $23.80 profit, 11.53%)
  Net profit:         $21.05 after fees

Vault 2  Balanced:
  USDT settled:        $8.40  [ Claim ]
  LOOP issued:         4.80 LOOP @ $1.000
  Fee paid:            $0.99
  Net profit:          $7.41

YOUR RATES:
  Vault 1: 11.53% (was 11.62% — improved ↓ 0.09pp)
  Vault 2: 11.91% (unchanged)
  Tip: 'Add $250 to Vault 2 this month to improve frequency score'

PROTOCOL:
  AUM: $712K  [73% to next fee drop at $1M]
  Protocol rate: 12.0% (unchanged)
  Ratchet: +0.75% fired 28 days ago  LOOP now worth $1.0075
  Next quarterly minimum: 62 days

CREDITS:
  No NFT — no credits this epoch
  [ Learn about Genesis NFTs ]
```

### 14.2 Email Notification — Opt-In

```
Subject: Your YieldLoop Epoch Summary — April 2025

Hi,

Here's your April summary:

VAULT 1 (Conservative):
  Profit this month: $23.80 USDT + 14.20 LOOP
  Fee paid: $2.75  |  Net: $21.05
  Rate improved: 11.62% → 11.53%

VAULT 2 (Balanced):
  Profit this month: $8.40 USDT + 4.80 LOOP
  Fee paid: $0.99  |  Net: $7.41

PROTOCOL UPDATE:
  AUM: $712K — 73% to the next fee drop
  LOOP ratchet: +0.75% fired this month
  Your LOOP is now worth $1.0075 each

[ View Dashboard ] [ Claim USDT ] [ View LOOP Portfolio ]
──────────────────────────────────────
All figures include fees. LOOP values include latest ratchet.
This is not financial advice.
Unsubscribe from epoch emails | Keep dashboard notifications
```

-----

## 15. Tax Export Screen

```
TRANSACTION HISTORY & TAX EXPORT

FILTER:
  Vault: [ All Vaults ▾ ] [ Vault 1 ▾ ] [ Vault 2 ▾ ]
  From: [ 2025-01-01 ]  To: [ 2025-12-31 ]
  Type: [ All ▾ ]

[ Download CSV — Tax Export ]  [ Download CSV — Full History ]

PREVIEW (last 5 transactions):

Date            Type              In            Out       USD Value
2025-04-30      LOOP_Issuance     14.20 LOOP    —         $14.20
2025-04-30      Fee_Deduction     —             $2.75     $2.75
2025-04-28      USDT_Claim        $23.80 USDT   —         $23.80
2025-04-14      Stop_Loss_Exec    $1,994.56     18.4 SOL  $1,994.56
2025-04-08      Deposit           —             $500 USDT $500.00

[ Load More ]

──────────────────────────────────────────────────────
⚠️  This export contains raw transaction data.
YieldLoop does not provide tax advice.
Consult a qualified tax professional in your jurisdiction.
```

-----

## 16. Referral Screen

```
MY REFERRAL CODE
  YL-7F3A-K9
  [ Copy Code ]  [ Share Link ]

HOW IT WORKS:
  1. Share your code with a friend
  2. They sign up and make their first deposit
  3. Exactly 30 days later — you both get $5 deposit credit

MY REFERRALS:
  Wallet 0x3a9f...  Joined: Mar 15  Credit: Pending (15 days left)
  Wallet 0x7c2b...  Joined: Feb 08  Credit: ✓ Paid ($5.00 applied)

MY CREDITS:
  Available: $5.00  (applied to your next deposit of $250+)
  Lifetime earned: $10.00
```

-----

## 17. Multi-Vault Dashboard

```
MY VAULTS  [ + Create New Vault ]

─────────────────────────────────────────────────────────────────
Vault 1  Conservative  $5,212  Genesis Maxi  2.5% eff  ● Active  ›
Vault 2  Balanced      $1,228  No NFT       11.91% eff ● Active  ›
─────────────────────────────────────────────────────────────────

COMBINED PORTFOLIO:
  Total balance:    $6,440  [$3,560 more for governance]
  Total USDT ready: $42.30  [ Claim All ]
  Total LOOP:       312.40 LOOP @ $1.008 = $314.90
  This month:       +$34.20 combined profit

OVERFLOW ROUTING:
  Vault 1 ceiling: $200K (standard)  Currently: 2.6%
  Vault 1 overflow → Vault 2
  Vault 2 ceiling: $200K (standard)  Currently: 0.6%
  Vault 2 overflow → External wallet (0x8b4f...)

GOVERNANCE:
  Combined balance: $6,440
  Status: Not yet eligible (need $10,000 OR Core/Maxi NFT)
  Vault 1 has Maxi NFT → [ Already qualifies via NFT ✓ ]
```

-----

## 18. Governance Screen

```
GOVERNANCE

YOUR STATUS: ✓ Eligible
  Vault 1 has Genesis Maxi NFT — governance qualified
  Combined balance: $6,440 (threshold: $10,000)

ACTIVE VOTES:
  [ Whitelist CAKE token for spot trading ]
    Proposed: 2025-04-01  |  Activates: 2025-04-08 if approved
    Votes: 1,842 yes / 234 no
    Your vote: [ ✓ Yes — voted Apr 03 ]

  [ LoopLab: $15,000 grant to Code Bridge Africa ]
    Proposed: 2025-04-10  |  Voting closes: 2025-04-17
    Votes: 892 yes / 147 no
    Your vote: [ Vote Yes ] [ Vote No ]

COMPLETED VOTES (last 3):
  LINK token whitelist — Approved — Activated Apr 01
  LoopLab $8,000 grant — Approved — Disbursed Mar 15
  AAVE token proposal — Rejected — Insufficient votes
```

-----

## 19. NFT Page

```
GENESIS NFT COLLECTION

┌──────────────┬──────────────┬──────────────┐
│  LITE        │  CORE        │  MAXI        │
│  $150        │  $1,000      │  $3,000      │
│  10% off     │  20% off     │  50% off     │
│  13 months   │  Lifetime    │  Lifetime    │
│  $200K vault │  $400K vault │  $1M vault   │
│  No credits  │  ~$8–38/mo   │  ~$20–94/mo  │
│  Clock runs  │  —           │  —           │
│  always      │              │              │
└──────────────┴──────────────┴──────────────┘

YOUR BREAK-EVEN (based on your Vault 1, rate 11.53%):
  Lite:  saves ~$174/yr → break-even ~10 months ✓
  Core:  saves ~$347/yr + ~$8/mo credits → ~21 months
  Maxi:  saves ~$867/yr + ~$20/mo credits → ~33 months

⚠️  Break-even at current rate — improves as protocol grows

[ Mint Lite ]   [ Mint Core ]   [ Mint Maxi ]

After minting: [ Apply to Vault ]
⚠️  Once applied, NFT is permanently bound to that vault
```

-----

## 20. LoopLab Page

```
LOOPLAB — BRIDGING THE DIGITAL DIVIDE
'Connecting underserved communities to technology —
 exposure, education, services, and opportunity.'

OUR MISSION:
  LoopLab is an independent organization focused on giving people
  who might never have access to technology a real pathway in.
  Starting locally in rural Maryland with broader regional reach,
  LoopLab works across four pillars:

  ▶ Tech Access — bringing technology within reach
  ▶ Tech Education — fintech, Web3, development, automation, robotics, drones
  ▶ Tech Services — practical solutions for local small businesses
  ▶ Tech Opportunities — real career and economic pathways

FUNDING MODEL — TWO REVENUE STREAMS:
  10% of all Genesis NFT sales → LoopLab
  10% of all platform fees → LoopLab

SELF-SUSTAINING BY DESIGN:
  Overflow funds are redirected into the YieldLoop system deposit,
  creating a compounding loop that funds operations indefinitely.

LIVE STATS:
  Total allocated to date: $[live figure]
  People reached:          [live figure]
  Businesses assisted:     [live figure]
  Programs active:         [live figure]

GOVERNANCE: Requires vault ownership with a good AI rating
and a Core or Maxi NFT

[ View Full Impact Report ]  [ Support LoopLab ]
```

-----

## 21. Emergency UI — Protocol Paused State

```
PROTOCOL PAUSED BANNER (full-width, red, cannot be dismissed):
  🔴 YieldLoop is temporarily paused
  'We are investigating an issue. Your funds are safe.
   No action is required from you.'
  Last updated: [timestamp]  Next update: [timestamp]
  [ View Status Page ]

DASHBOARD DURING PAUSE:
  All vault balances: visible ✓
  USDT ready:         visible ✓  [ Claim ] DISABLED
  LOOP portfolio:     visible ✓  [ Redeem ] DISABLED
  New deposits:       DISABLED — greyed, tooltip explains
  New withdrawals:    DISABLED — greyed, tooltip explains
  Queued withdrawals: visible, status shown, processing continues

  'Your positions are held safely during the pause.
   No trades are being executed.
   All LP and staking positions remain active.'

AFTER RESUME:
  Green banner: '✓ Protocol resumed — all operations active'
  Brief explanation of what happened
  Banner dismissible after 24 hours
```

> **DURING PAUSE:** Dashboard must remain fully readable. Users must be able to see their balances, LOOP, and queued withdrawals at all times. They just cannot initiate new actions. Hiding data during a pause destroys trust.

-----

## 22. Dashboard Alerts Reference

|Alert Type                   |Display                             |
|-----------------------------|------------------------------------|
|Protocol rate dropped        |GREEN full-width banner — persistent|
|Near AUM milestone           |TEAL card                           |
|Vault accelerator improved   |TEAL toast                          |
|Vault dormant                |AMBER card on vault                 |
|Gas low                      |AMBER toast                         |
|Near vault ceiling           |TEAL card                           |
|Stop-loss executed           |RED toast + email always            |
|Take-profit executed         |GREEN toast                         |
|Ratchet fired                |GREEN toast + dashboard             |
|Quarterly minimum approaching|AMBER card (>75 days)               |
|Referral credit earned       |TEAL toast                          |
|New token proposed           |TEAL card                           |
|Epoch summary ready          |TEAL card                           |
|Protocol paused              |RED full-width — not dismissible    |
|Governance vote open         |TEAL card                           |

-----

## 23. Non-Negotiable Implementation Rules

The following rules cannot be overridden by any team decision:

1. All three rates shown per-vault: protocol base, vault rate, effective rate
1. Fee trajectory shown with mandatory ‘estimated — not guaranteed’ caveat
1. AUM progress to next milestone always visible on dashboard
1. Accelerator tab shows all four signal scores with improvement tips
1. No APY projection without ‘estimated — not guaranteed’ label
1. No LOOP value shown without ‘internal accounting unit — no external value’
1. Immediate exit: past-epoch LOOP shown GREEN + SAFE, current-epoch RED + FORFEITED
1. Immediate exit: arb settlement shown as separate line, no premium
1. Immediate exit: ‘Go Back’ equal or more prominent than ‘Exit Now’
1. Acknowledgment: NEVER pre-ticked, NO ‘select all’, paste disabled, autocomplete off
1. Acknowledgment: wallet recovery/death box cannot be removed or reworded
1. Backup phrase: user must type back 2 randomly selected words to confirm
1. Protocol paused: banner cannot be dismissed, dashboard remains readable
1. Stop-loss notification: always sent regardless of email opt-in setting
1. Fee estimator: ‘not guaranteed’ caveat same visual weight as numbers
1. Break-even on NFT page: ‘at current rate — improves as protocol grows’ required
1. Sanctions screening: every wallet connection — never session-cached
1. Governance eligibility: shown on dashboard — both paths (balance + NFT) displayed
1. LoopLab: accessible from main nav — not buried in footer
1. Tax export: always available, never restricted, no minimum period

-----

*YieldLoop UI/UX Specification v5.2*
