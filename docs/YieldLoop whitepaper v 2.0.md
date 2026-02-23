**YIELDLOOP**

*Multi-Chain Structured Profit Engine*

**Whitepaper v2.0**

Todd Koletsky \| HT1 (SW), USN (Ret.) \| Systems Architect

  -------------- -------------- -------------- -------------- --------------
  **BSC Hub**    **Base**       **Arbitrum**   **Polygon**    **Ethereum**

  -------------- -------------- -------------- -------------- --------------

+-----------------+-----------------+-----------------+-----------------+
| **LOOP Token**  | **4 Income      | **Self-Healing  | **Fail-Closed   |
|                 | Streams**       | Ops**           | Safety**        |
| Canonical on    |                 |                 |                 |
| BSC             | Real yield only | Friction Pool   | Inv             |
|                 |                 |                 | ariant-enforced |
+-----------------+-----------------+-----------------+-----------------+

*YieldLoop does not promise yield. It enforces surplus-backed growth
under strict liability isolation,*

*disciplined four-source passive income, and a self-healing friction
pool that ensures uninterrupted vault operation.*

**0 Executive Summary**

YieldLoop is a multi-chain, epoch-based profit engine that converts
realized DeFi income into a non-transferable redemption certificate
(LOOP), backed 1:1 by closed profit and strengthened through a tiered
deterministic ratchet mechanism. Users deposit on any supported chain
--- BNB Chain, Base, Arbitrum, Polygon, or Ethereum --- through a
hub-and-spoke architecture that routes all capital to BSC for unified
strategy execution, then returns rewards to the user\'s chain of choice.

**0.1 What\'s New in v2.0**

-   Multi-chain deposit and withdrawal support: BSC, Base, Arbitrum,
    Polygon, Ethereum

-   Cross-chain bridge layer via Stargate (LayerZero) for native USDT
    transfers

-   LOOP remains canonical on BSC with wLOOP bridge wrappers for other
    chains

-   Spoke Gateway contracts on each chain for trustless deposit routing

-   Bridge fees classified as operational costs, funded by Friction Pool

-   Cross-chain risk disclosures added to Legal & Governance framework

**0.2 Four Income Streams**

  ------------------ ----------------------------- ----------------------
  **Income Stream**  **Description**               **Platforms**

  1\. Active Trading Cross-DEX arbitrage and spot  PancakeSwap ↔ BiSwap
                     profit execution              (BSC)

  2\. Liquidity      DEX LP fees from concentrated PancakeSwap v3, Thena
  Provision          liquidity positions           

  3\. Stablecoin     Auto-compounding stable       Beefy Finance, Alpaca
  Yield Vaults       strategies                    Finance

  4\. BNB Liquid     Staking rewards --- liquid,   Ankr aBNBc, pStake
  Staking            no lockup                     stkBNB
  ------------------ ----------------------------- ----------------------

**0.3 Core Invariants**

-   LOOP mints only from realized investment profit --- never from
    friction pool yield

-   Coverage_ratio_E ≥ 1.0 at all times --- system fails closed before
    coverage breaks

-   Redemption price is permanently non-decreasing

-   Epochs fully isolated --- no cross-epoch subsidy ever

-   Friction pool capital segregated from AUM at all times

-   Friction debt is non-waivable and fully visible

-   No leverage --- never borrows against deposited assets

**1 Vision & Design Philosophy**

**1.1 Problem Statement**

DeFi has repeatedly failed users through inflationary models, blended
liabilities, opaque treasuries, and unsustainable yield. A second
failure class --- operational fragility --- is less discussed but just
as damaging: systems that work mathematically but break practically
because users run out of gas, miss redemption windows, or have passive
income claims fail silently. YieldLoop solves both classes of failure.

A third problem is accessibility. The most disciplined DeFi protocols
have historically been single-chain, requiring users to bridge manually,
manage gas on unfamiliar chains, and reconcile cross-chain accounting
themselves. YieldLoop v2.0 eliminates this barrier by handling all
cross-chain complexity at the protocol layer.

**1.2 Core Vision**

YieldLoop is a structured, epoch-based profit engine accessible from any
major EVM chain. It generates income through four disciplined sources,
mints LOOP only from realized profit, isolates liabilities by calendar
epoch, uses a tiered micro-tick ratchet, enforces tiered holding with
structured early exit, and maintains a self-healing friction pool that
keeps vaults running without user intervention.

**1.3 Foundational Principles**

**1. Deterministic Accounting**

All LOOP minted represents closed, realized, on-chain income. No forward
yield. No emissions. No speculative inflation.

**2. Epoch Isolation**

Each month is a separate liability bucket. No cross-subsidization. No
blending. Each epoch stands on its own math.

**3. Coverage Before Growth**

Redemption coverage must always be maintained. Ratchet tiers activate
only when surplus supports them.

**4. Four-Source Income Discipline**

All four income sources governed by the same conservative rules. No
leverage. No unaudited platforms. No speculative positions.

**5. Operational Self-Sufficiency**

A vault should never stop working because of a gas or bridge fee
problem. The Friction Pool ensures uninterrupted operation through
adaptive sizing, idle yield, intelligent borrowing, and automatic debt
servicing.

**6. Chain Agnosticism**

Users should be able to deposit from, and receive rewards on, any major
EVM chain. Cross-chain complexity is a protocol responsibility, not a
user responsibility.

**7. Fail-Closed Safety**

If invariants are threatened, all affected modules pause. The system
halts before insolvency is possible.

**2 Multi-Chain Architecture**

**2.1 Hub-and-Spoke Model**

YieldLoop uses a hub-and-spoke architecture. BSC is the hub --- all
strategy execution, vault accounting, LOOP minting, ratchet logic,
coverage ratios, and epoch accounting live exclusively on BSC. This
preserves invariant integrity: there is exactly one source of truth for
every financial calculation.

Base, Arbitrum, Polygon, and Ethereum are spokes. Each spoke hosts a
lightweight SpokeGateway contract that accepts deposits, bridges them to
BSC via Stargate, and handles withdrawal routing back to the user\'s
chain.

+-----------------------------------------------------------------------+
| **■ Hub (BSC): Vault accounting, LOOP minting, ratchet, coverage,     |
| epoch isolation, strategy execution**                                 |
|                                                                       |
| **■ Spokes (Base, Arbitrum, Polygon, ETH): Deposit ingress,           |
| withdrawal egress, bridge interface only**                            |
|                                                                       |
| **■ Bridge Layer: Stargate (LayerZero) --- native USDT transfers,     |
| battle-tested, no wrapped intermediaries**                            |
|                                                                       |
| **■ LOOP: Canonical on BSC. wLOOP available on spokes via bridge      |
| wrapper for users who prefer another chain**                          |
+-----------------------------------------------------------------------+

**2.2 Supported Chains**

  ------------- -------------------- ------------ -------------- ------------
  **Chain**     **Role**             **Native Gas **USDT         **Status**
                                     Token**      Standard**     

  BNB Chain     Hub --- all strategy BNB          BSC USDT       Live
  (BSC)         execution                         (BEP-20)       

  Base          Spoke ---            ETH          USDT (ERC-20)  Live
                deposit/withdrawal                               

  Arbitrum      Spoke ---            ETH          USDT (ERC-20)  Live
                deposit/withdrawal                               

  Polygon       Spoke ---            MATIC/POL    USDT (ERC-20)  Live
                deposit/withdrawal                               

  Ethereum      Spoke ---            ETH          USDT (ERC-20)  Live
                deposit/withdrawal                               
  ------------- -------------------- ------------ -------------- ------------

**2.3 SpokeGateway Contract**

Each spoke chain hosts a SpokeGateway contract. Its responsibilities are
minimal by design:

-   Accept USDT deposits from users on that chain

-   Call Stargate router to bridge USDT to BSC Hub Vault

-   Pass referral data and vault configuration through the bridge
    message

-   Receive withdrawal signals from BSC and execute the outbound
    transfer

-   Register referral relationships (passed to BSC for on-chain
    enforcement)

The SpokeGateway holds no vault accounting, no LOOP balances, and no
epoch state. It is a bridge interface, not a financial system.

**2.4 Cross-Chain Deposit Flow**

+-----------------------------------------------------------------------+
| **Step 1: User approves USDT on their chain (Base, Arb, Polygon, or   |
| ETH)**                                                                |
|                                                                       |
| **Step 2: User calls SpokeGateway.depositToVault(amount, vaultId,     |
| referrer)**                                                           |
|                                                                       |
| **Step 3: SpokeGateway calls Stargate router --- USDT bridges to      |
| BSC**                                                                 |
|                                                                       |
| **Step 4: BSC HubVault receives bridged USDT, registers vault         |
| position**                                                            |
|                                                                       |
| **Step 5: Vault confirmation appears in user dashboard across all     |
| chains**                                                              |
|                                                                       |
| **Bridge fee source: Friction Pool (classified as operational cost)** |
|                                                                       |
| **Bridge settlement: typically 1--5 minutes via Stargate/LayerZero**  |
+-----------------------------------------------------------------------+

**2.5 Cross-Chain Withdrawal & Reward Delivery**

At cycle close, users select their reward format (USDT or LOOP) and
their delivery chain. The BSC hub processes the distribution and routes
accordingly:

  --------------------- -------------------------------------------------
  **User Preference**   **What Happens**

  USDT on Base          BSC vault releases USDT → Stargate bridges to
                        Base → delivered as native USDT

  USDT on Arbitrum      Same flow, Arbitrum destination

  USDT on Ethereum      Same flow, Ethereum destination

  LOOP on BSC           Minted on BSC, stays on BSC --- no bridge needed

  wLOOP on              Minted on BSC → bridged as wLOOP → delivered on
  Base/Arb/Poly/ETH     destination chain
  --------------------- -------------------------------------------------

**2.6 LOOP Token: Canonical Chain Model**

LOOP is canonical on BSC. This is a non-negotiable architectural
decision. LOOP\'s floor price is tied to a non-decreasing invariant
backed by real vault surplus. Replicating native LOOP across five chains
would create five independent accounting systems that must stay
synchronized --- a coordination problem that breaks under stress and
creates attack surface.

Users on other chains who hold LOOP receive wLOOP (wrapped LOOP): a 1:1
bridge-backed representation redeemable on BSC at any time. The user
interface displays a unified \'LOOP balance\' regardless of chain ---
the wrapping is invisible to the end user.

+-----------------------------------------------------------------------+
| **■ LOOP minted: BSC only --- always**                                |
|                                                                       |
| **■ LOOP redeemed: BSC only --- always**                              |
|                                                                       |
| **■ Floor price tracked: BSC only --- single source of truth**        |
|                                                                       |
| **■ wLOOP on other chains: bridge-backed 1:1 IOU, redeemable on BSC** |
|                                                                       |
| **■ Invariant integrity: preserved --- no multi-chain floor desync    |
| risk**                                                                |
+-----------------------------------------------------------------------+

**2.7 Bridge Risk & Operational Cost Classification**

Cross-chain bridging introduces third-party protocol risk.
Stargate/LayerZero has its own smart contract risk, relayer risk, and
oracle assumptions. These are disclosed in the Legal & Governance
document. Users acknowledge bridge risk as part of multi-chain
participation.

Bridge fees are classified as operational costs and funded by the
Friction Pool --- the same classification as gas fees, DEX swap fees,
and AI compute. This keeps AUM accounting clean and ensures bridge costs
never appear as a surprise deduction from user principal.

**3 System Architecture Overview**

**3.1 Module Stack**

  ----------- ------------------ ------------------------------------------------
  **Layer**   **Module**         **Description**

  0           Multi-Chain Access SpokeGateway contracts on Base, Arb, Polygon,
              Layer              ETH + Stargate bridge

  1           Wallet & Access    Web3, Privy (embedded wallets), Transak (fiat
              Layer              on-ramp)

  2           Vault Layer (BSC   Capital allocation, cycle participation, epoch
              Hub)               accounting

  3           Friction Pool      Per-vault operational reserve --- segregated
                                 from AUM

  4           Active Trading     Cross-DEX arbitrage + spot (PancakeSwap ↔
              Engine             BiSwap)

  5           Passive Income     LP + Stablecoin Vaults + BNB Staking
              Layer              

  6           Epoch Mint Engine  LOOP minting from realized profit only

  7           Tiered Ratchet &   Coverage-gated micro-tick floor appreciation
              Redemption         

  8           Claim & Redemption Per-epoch portal with penalty preview and debt
              Portal             display

  9           NFT Program Layer  Fee reduction and governance weight

  10          AI Governance      Bounded optimization --- cannot override
              Layer              invariants
  ----------- ------------------ ------------------------------------------------

**3.2 Vault Layer**

Minimum 500 USDT to create a vault; 250 USDT for subsequent deposits.
Deposits arriving via SpokeGateway from other chains are treated
identically to direct BSC deposits --- they are registered at the BSC
Hub vault with the same rules. At vault creation and each top-up, the
user is prompted to fund the Friction Pool before finalizing the
deposit. Vault AUM and Friction Pool are fully segregated --- friction
pool capital is never counted as AUM.

**3.3 Passive Income Layer**

  ---------------- ---------------------- -------------------------------
  **Source**       **Platform(s)**        **Income Type**

  Liquidity        PancakeSwap v3, Thena  DEX trading fees (net of IL)
  Provision                               

  Stablecoin Yield Beefy Finance, Alpaca  Auto-compounding vault APY
  Vaults           Finance                

  BNB Liquid       Ankr aBNBc, pStake     BNB staking rewards --- liquid
  Staking          stkBNB                 
  ---------------- ---------------------- -------------------------------

**4 Monthly Execution Engine**

**4.1 Five-Phase Cycle**

  ------------- ------------- -------------------------------------------
  **Phase**     **Timing**    **Activity**

  1 --- Deposit Before 1st    Vault setup, friction pool top-up prompt,
  & Strategy                  AI config, strategy lock

  2 --- Active  1st -- last   Trading + all passive sources + friction
  Window        day           pool idle deployment

  3 --- Cycle   Last day      Positions closed, fees claimed, friction
  Close         23:59 UTC     pool yield handled separately

  4 --- Mint &  1st of next   Friction debt serviced first, LOOP minted,
  Ratchet       month         tiered ratchet runs

  5 ---         1st of month  Standard (window) + early redemption
  Redemption                  (anytime after 30 days)
  Window                      
  ------------- ------------- -------------------------------------------

**4.2 Profit Calculation Formula**

+-----------------------------------------------------------------------+
| Realized_Profit_E =                                                   |
|                                                                       |
| Trading_Profit_E                                                      |
|                                                                       |
| \+ (LP_Fees_E − Impermanent_Loss_E)                                   |
|                                                                       |
| \+ Stablecoin_Vault_Yield_E \[vault AUM portion only\]                |
|                                                                       |
| \+ BNB_Staking_Rewards_E \[vault AUM portion only\]                   |
|                                                                       |
| Friction_Pool_Yield_E → excluded from above                           |
|                                                                       |
| → Reduces friction pool minimum target, then credits to user as USDT  |
+-----------------------------------------------------------------------+

**4.3 Phase 4 --- Debt Service Before LOOP Mint**

+-----------------------------------------------------------------------+
| **If Friction_Debt \> 0:**                                            |
|                                                                       |
| Interest = Friction_Debt × 0.005                                      |
|                                                                       |
| Total_owed = Friction_Debt + Interest                                 |
|                                                                       |
| Confiscated = min(Total_owed, User_Net_Profit_E)                      |
|                                                                       |
| Route confiscated amount to friction pool + lending source            |
|                                                                       |
| Remaining profit proceeds to LOOP mint or USDT claim                  |
|                                                                       |
| Unserviced debt carries to next cycle                                 |
+-----------------------------------------------------------------------+

**5 LOOP Epoch Liability Model**

**5.1 Epoch State Variables**

Each calendar month is an isolated Epoch (E) recording: LOOP_supply_E,
Redemption_pool_E, Ratchet_pool_E, Redemption_price_E, Coverage_ratio_E.
No values shared across epochs. Friction pool state tracked per vault,
not per epoch.

**5.2 LOOP Minting Rule**

LOOP mints only when Realized_Profit_E \> 0, from the four investment
income sources only. Friction pool yield is explicitly excluded. Minting
is 1:1 with net profit after fee. No emission schedule. No speculative
minting. All minting occurs on BSC regardless of which chain the user
deposited from.

**5.3 Coverage Invariant**

+-----------------------------------------------------------------------+
| Coverage_ratio_E = Redemption_pool_E ÷ (LOOP_supply_E ×               |
| Redemption_price_E)                                                   |
|                                                                       |
| **Invariant: Coverage_ratio_E ≥ 1.0 at all times**                    |
|                                                                       |
| Redemption fails if post-execution Coverage_ratio_E \< 1.0            |
+-----------------------------------------------------------------------+

**5.4 Friction Pool Segregation Invariant**

Friction Pool capital is fully segregated from vault AUM at all times.
Never counted in vault value, never in Realized_Profit_E, never in
coverage ratio calculations. Friction pool yield does not flow through
the performance fee.

**6 Tiered Ratchet Growth Mechanism**

**6.1 Micro-Tick Architecture**

+-----------------------------------------------------------------------+
| Micro_tick = 0.000025 USDT                                            |
|                                                                       |
| Redemption_price_E displayed at 6 decimal places                      |
|                                                                       |
| Example progression: 1.000000 → 1.000025 → 1.000050 → \...            |
|                                                                       |
| **1,000 LOOP: each micro-tick = +\$0.025 redeemable value**           |
|                                                                       |
| **10,000 LOOP: each micro-tick = +\$0.250 redeemable value**          |
+-----------------------------------------------------------------------+

**6.2 Three Surplus Tiers**

  ----------------- ----------------- ----------------- -----------------
  **Tier**          **Coverage        **Ticks /         **USDT
                    Ratio**           Evaluation**      Increment**

  Below threshold   \< 1.02           0 (ratchet        No movement
                                      pauses)           

  Tier 1 --- Micro  1.02 -- 1.04      1 tick            0.000025 USDT

  Tier 2 ---        1.05 -- 1.09      4 ticks           0.000100 USDT
  Standard                                              

  Tier 3 ---        1.10 and above    10 ticks          0.000250 USDT
  Accelerated                                           
  ----------------- ----------------- ----------------- -----------------

**6.3 Non-Decreasing Guarantee**

Redemption_price_E may only increase or remain constant. It may never
decrease under any condition --- not in a bad month, not during a
drawdown, not by governance vote. Enforced at the contract invariant
level.

**7 Friction Pool Architecture**

The Friction Pool is a per-vault operational reserve that funds all
protocol interaction costs, maintains itself through idle yield
generation, and self-heals through a structured borrow-and-service
mechanism when depleted. In v2.0, the Friction Pool also covers
cross-chain bridge fees for spoke deposits and withdrawals.

**7.1 What the Friction Pool Covers**

  ------------------- ---------------------------------------------------
  **Cost Category**   **Description**

  Transaction gas     BNB gas for all on-chain interactions on BSC Hub

  Cross-chain bridge  Stargate/LayerZero fees for spoke deposits and
  fees                withdrawals

  DEX swap fees       Entry and exit costs for passive income position
                      management

  Protocol            Fees on Beefy, Alpaca, Ankr, pStake
  interaction fees    

  LP rebalancing      Gas for range management at cycle boundaries
  costs               

  Emergency unwinding Gas and slippage for emergency position closures
  costs               

  AI compute costs    GPT-4o-mini API usage for strategy optimization

  Ratchet evaluation  Gas for monthly computation and on-chain price
  gas                 update

  Redemption window   Gas for redemption execution on behalf of user
  gas                 
  ------------------- ---------------------------------------------------

**7.2 Adaptive Sizing Model**

+-----------------------------------------------------------------------+
| Target_balance = max(AUM_band_minimum, Avg_monthly_friction_spend ×   |
| 2.0)                                                                  |
|                                                                       |
| **AUM Band Minimums (used until 3 cycles of history):**               |
|                                                                       |
| **AUM \< \$1,000: \$10 USDT**                                         |
|                                                                       |
| **AUM \$1,000 -- \$5,000: \$15 USDT**                                 |
|                                                                       |
| **AUM \$5,000 -- \$20,000: \$25 USDT**                                |
|                                                                       |
| **AUM \$20,000 -- \$100,000: \$50 USDT**                              |
|                                                                       |
| **AUM \> \$100,000: \$100 USDT + \$10 per additional \$50k AUM**      |
|                                                                       |
| **After 3 cycles: Target = max(AUM_band_minimum, Avg_monthly_spend ×  |
| 2.0)**                                                                |
|                                                                       |
| System never holds materially more than 60 days of projected spend    |
+-----------------------------------------------------------------------+

**7.3 Borrow Priority Order**

When the liquid buffer is insufficient to cover an operational cost, the
system borrows automatically in this order:

-   Source 1: User\'s unrealized USDT claim balance

-   Source 2: User\'s Ratchet_pool_E share (up to 20% of user\'s share)

-   Source 3: Protocol credit line (System Vault funded)

-   If all insufficient: vault pauses, user must top up manually

Hard cap: Total_Friction_Debt ≤ 5% of Vault_AUM at all times. Exceeding
cap: vault pauses immediately.

**7.4 Debt Servicing Mechanisms**

Three mechanisms exist in order of precedence: (1) Automatic profit
confiscation at cycle close --- outstanding debt plus interest is
deducted before any LOOP is minted or USDT credited. (2) Voluntary
repayment --- user may top up and earmark excess as debt repayment at
any time. (3) Withdrawal intercept --- if a withdrawal is requested
before debt is cleared, the outstanding amount is deducted from proceeds
before release.

Friction debt is a protocol obligation. It cannot be waived by
governance vote, AI decision, admin action, or user request. This is a
hard invariant.

**8 Performance Fee Allocation & Treasury Policy**

+-----------------------------------------------------------------------+
| Performance_Fee_E = Realized_Profit_E × 0.10                          |
|                                                                       |
| **Applies to: Trading + LP (net) + Stablecoin Vaults + BNB Staking**  |
|                                                                       |
| **Excluded: Friction Pool yield**                                     |
+-----------------------------------------------------------------------+

  ---------------------- ---------------- -------------------------------------
  **Allocation**         **Percentage**   **Notes**

  Ratchet_pool_E         60%              Isolated per epoch --- funds floor
  (epoch-specific)                        appreciation

  Operations &           20%              Protocol maintenance, engineering,
  Development                             infrastructure

  Marketing              10%              Includes Referral Pool (40% of
                                          marketing allocation)

  LoopLab                10%              Real-world impact funded from real
                                          surplus --- not emissions
  ---------------------- ---------------- -------------------------------------

**9 Tiered Holding Periods & Early Redemption**

**9.1 Standard Holding Tiers**

  --------------- --------------- ------------- ----------------------------
  **Tier**        **LOOP          **Hold        **Rationale**
                  Balance**       Period**      

  Micro           \< 500 LOOP     45 days       Retail accessible

  Standard        500 -- 4,999    75 days       Balanced flexibility
                  LOOP                          

  Extended        5,000 -- 24,999 90 days       Original 3-month discipline
                  LOOP                          

  Institutional   25,000+ LOOP    120 days      Material liability impact
  --------------- --------------- ------------- ----------------------------

**9.2 Early Redemption --- Penalty Structure**

  ------------- ----------- ---------------------- -----------------------
  **Days        **Penalty   **Example (price =     **Routes To**
  Remaining**   Rate**      \$1.05)**              

  61+ days      15%         User receives \$0.8925 Ratchet_pool_E
  early                     / LOOP                 

  31--60 days   10%         User receives \$0.9450 Ratchet_pool_E
  early                     / LOOP                 

  11--30 days   5%          User receives \$0.9975 Ratchet_pool_E
  early                     / LOOP                 

  1--10 days    2%          User receives \$1.0290 Ratchet_pool_E
  early                     / LOOP                 

  0 days        0%          User receives \$1.0500 N/A
  (eligible)                / LOOP                 
  ------------- ----------- ---------------------- -----------------------

Early exit penalties route 100% to Ratchet_pool_E. This is structurally
positive for remaining holders: ratchet pool grows, LOOP supply shrinks,
future ticks become cheaper. Early exits strengthen remaining positions.

**10 AI Governance & Control Boundaries**

**10.1 AI May**

-   Propose and adjust trade parameters, LP ranges, vault allocations,
    BNB staking split within approved bounds

-   Propose friction pool idle deployment allocation (stablecoin vault
    vs BNB staking)

-   Flag underperforming positions, out-of-range LP, anomalies across
    all sources

-   Recommend pauses under abnormal conditions

-   Receive structured JSON inputs --- vault AUM, volatility metrics, LP
    APR, IL estimates, stablecoin APY, friction pool balance

**10.2 AI May Not**

-   Mint LOOP, change fees, modify redemption formulas, cross-subsidize
    epochs

-   Override coverage invariants or the non-decreasing ratchet guarantee

-   Use leverage in any deployment including friction pool

-   Deposit to unapproved platforms or execute mid-cycle reallocation

-   Authorize friction pool borrowing beyond defined limits or waive
    friction debt terms

-   Override any hard-coded invariant regardless of instruction source

**10.3 Governance Hierarchy**

  ----------- --------------- ------------------------------------ -------------------
  **Level**   **Authority**   **Scope**                            **Overrideable?**

  1           Hard-coded      Coverage, isolation, non-decreasing  No
              invariants      price, profit-only minting, friction 
                              pool segregation                     

  2           Smart contract  All invariants at contract level     No
              enforcement                                          

  3           Governance      Risk caps, ratchet thresholds,       NFT holders only
              bounds          friction pool parameters (within     
                              guardrails)                          

  4           AI optimization Trade params, LP ranges, vault       Within Level 3 only
                              selection, friction pool idle        
                              deployment                           
  ----------- --------------- ------------------------------------ -------------------

**10.4 AI Model**

Primary: OpenAI GPT-4o-mini. Fallback: same model, separate API key.
Final fallback: deterministic safe preset rules engine. AI cost funded
by Friction Pool. Estimated monthly AI cost per vault: under \$1 under
normal operation.

**11 Complete System Invariants**

  -------- ----------------- ----------------------------------------------------
  **\#**   **Invariant**     **Rule**

  1        LOOP minting      From realized investment profit only --- never from
                             friction pool yield

  2        Redemption price  Non-decreasing, never reversible under any condition

  3        Coverage ratio    Coverage_ratio_E ≥ 1.0 at all times

  4        Redemption        May not violate coverage invariant
           execution         

  5        Ratchet trigger   Requires Coverage_ratio_E ≥ 1.02 minimum

  6        Ratchet           Post-transfer Coverage_ratio_E must remain ≥ 1.02
           post-check        

  7        Epoch isolation   No cross-epoch subsidy of any kind

  8        LOOP              Non-transferable, epoch-tagged, canonical on BSC
           transferability   

  9        Holding tiers     Tiered minimum hold period governs standard
                             redemption

  10       Early redemption  Routes 100% to Ratchet_pool_E of that epoch
           penalty           

  11       No leverage       No borrowing against deposited assets --- ever

  12       Platform          Approved list only; audit criteria enforced
           whitelist         

  13       Mid-cycle         Not permitted
           reallocation      

  14       Friction pool     Never counted as AUM, never in coverage calculations
           segregation       

  15       Friction pool     Not subject to performance fee; returns to pool or
           yield             user directly

  16       Friction debt cap Max 5% of vault AUM; beyond this vault pauses

  17       Friction debt     Visible on dashboard immediately upon borrow
           visibility        

  18       Friction debt     Serviced before LOOP mint, USDT credit, or
           servicing         withdrawal release

  19       Friction debt     0.5%/month; routes to lending pool that provided
           interest          credit

  20       Friction pool     Friction pool capital never deployed to LP positions
           no-LP             

  21       Friction debt     Cannot be waived by governance, AI, admin, or user
           non-waivable      

  22       AI bounds         Cannot override any invariant

  23       Fail-closed       System halts before insolvency risk emerges

  24       LOOP canonical    LOOP minted and redeemed on BSC only --- single
           chain             source of truth

  25       Bridge fee        Cross-chain bridge fees funded by Friction Pool, not
           classification    AUM
  -------- ----------------- ----------------------------------------------------

**12 Sustainability & Coverage Model**

**12.1 Four-Source Income Redundancy**

  --------------- ------------- ----------- -------------------------------
  **Condition**   **Trading**   **LP +      **BNB Staking / Net Outcome**
                                Vaults**    

  Normal market   Active        Active      All four generating

  Low volatility  Reduced       Vaults      Vaults + staking provide floor
                                active      

  High volatility May pause     LP may gap  Staking + vault APY continues

  Bear market     Reduced       Fees lower  Stablecoin holds

  All sources     Zero          Zero        True zero-profit month ---
  flat                                      floor pauses, nothing breaks
  --------------- ------------- ----------- -------------------------------

**12.2 No Reflexive Spiral**

LOOP is non-transferable (no panic market). No rebasing. No emissions.
No cross-epoch blending. Friction debt capped at 5% of AUM. Liability
expansion tied only to realized investment profit. Multi-chain access
does not change these properties --- the hub-and-spoke model ensures all
accounting remains on BSC under unified rules.

**12.3 LoopLab**

LoopLab is the proof-of-impact component of YieldLoop. A portion of real
profit --- 10% of the performance fee --- funds real-world initiatives.
Not from token printing. Not from emissions. Only from actual earned
surplus. If YieldLoop grows responsibly, LoopLab grows responsibly.

**13 Design Philosophy --- Closing Statement**

YieldLoop v2.0 adds one significant new dimension to the architecture:
universal accessibility. Previous versions established the financial
model --- epoch isolation, surplus-backed ratchets, realized-profit-only
minting, passive income from four sources, and the self-healing Friction
Pool. Version 2.0 establishes that the system should be accessible from
any major EVM chain without compromising any of those properties.

The hub-and-spoke model is not a shortcut --- it is the only
architecturally sound path. A single BSC hub with LOOP canonical on one
chain means exactly one source of truth for every financial invariant.
Users on Base, Arbitrum, Polygon, or Ethereum participate in the same
system, under the same rules, with the same protections.

+-----------------------------------------------------------------------+
| **What has changed in v2.0:**                                         |
|                                                                       |
| → Multi-chain deposit and withdrawal via Stargate hub-and-spoke       |
|                                                                       |
| → SpokeGateway contracts on Base, Arbitrum, Polygon, Ethereum         |
|                                                                       |
| → wLOOP bridge wrappers for users who prefer non-BSC reward delivery  |
|                                                                       |
| → Bridge fees classified as Friction Pool operational costs           |
|                                                                       |
| → Cross-chain risk disclosures added to governance framework          |
|                                                                       |
| **What has not changed:**                                             |
|                                                                       |
| → LOOP mints only from realized investment profit                     |
|                                                                       |
| → Coverage_ratio_E ≥ 1.0 at all times                                 |
|                                                                       |
| → Epochs fully isolated --- no cross-subsidy ever                     |
|                                                                       |
| → The ratchet floor never retreats                                    |
|                                                                       |
| → The system fails closed before it fails insolvent                   |
|                                                                       |
| → Discipline is enforced by code                                      |
+-----------------------------------------------------------------------+

***Growth is a byproduct of discipline.***

*Stability is the primary objective.*

*Accessibility is the promise to every user, regardless of chain.*

**YieldLoop Whitepaper v2.0**

Author: Todd Koletsky, HT1 (SW), USN (Ret.) --- Systems Architect

BNB Chain Hub \| Base \| Arbitrum \| Polygon \| Ethereum \| LOOP Token
