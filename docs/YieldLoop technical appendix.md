**YIELDLOOP**

**Technical Appendix v2.0**

*Engineering & Implementation Reference*

Audience: Engineering / Auditors / Implementation

+-----------------------------------------------------------------------+
| This document contains:                                               |
|                                                                       |
| → Multi-Chain Architecture: SpokeGateway contracts, Stargate bridge   |
| integration, wLOOP model                                              |
|                                                                       |
| → Referral Module: Full on-chain state machine, qualification logic,  |
| credit lifecycle                                                      |
|                                                                       |
| → AI Architecture: Model selection, structured I/O, cost control,     |
| deterministic safety layer                                            |
|                                                                       |
| → Smart Contract Specs: State variables, invariant enforcement, event |
| requirements                                                          |
|                                                                       |
| → Testing Checklists: All modules covered                             |
|                                                                       |
| All invariants are enforced on-chain. Off-chain components (AI,       |
| bridge relayer, automation bot) cannot override protocol rules.       |
+-----------------------------------------------------------------------+

**A Multi-Chain Technical Specification**

**A.1 Architecture Overview**

BSC is the canonical hub for all vault accounting, LOOP minting, epoch
state, and invariant enforcement. Spoke chains (Base, Arbitrum, Polygon,
Ethereum) host minimal SpokeGateway contracts that serve as deposit
entry points and withdrawal exit points only. No vault logic lives on
spoke chains.

**A.2 Bridge Layer: Stargate (LayerZero)**

Stargate handles native USDT transfers across all supported chains. It
is chosen over alternatives because it supports native (non-wrapped)
USDT on BSC, Base, Arbitrum, Polygon, and Ethereum; it is battle-tested
with substantial TVL; and its composable messaging allows arbitrary data
(vaultId, referrer address) to be passed alongside the token transfer.

  ---------------------- ------------------------------------------------
  **Property**           **Value**

  Bridge protocol        Stargate Finance (LayerZero)

  Token transferred      Native USDT on each chain

  Additional message     vaultId, referrer address, reward delivery chain
  payload                preference

  Settlement time        Typically 1--5 minutes

  Fee funding source     Friction Pool (operational cost)

  Fallback on bridge     Transaction reverts --- no partial state written
  failure                
  ---------------------- ------------------------------------------------

**A.3 SpokeGateway Contract Interface**

+-----------------------------------------------------------------------+
| // SpokeGateway.sol --- deployed on Base, Arbitrum, Polygon, Ethereum |
|                                                                       |
| interface ISpokeGateway {                                             |
|                                                                       |
| // Deposit USDT and bridge to BSC Hub Vault                           |
|                                                                       |
| // amount: USDT amount (6 decimals)                                   |
|                                                                       |
| // vaultId: 0 for new vault creation, existing ID for top-up          |
|                                                                       |
| // referrer: address of referrer (address(0) if none)                 |
|                                                                       |
| // rewardChain: chain ID for reward delivery                          |
|                                                                       |
| function depositToVault(                                              |
|                                                                       |
| uint256 amount,                                                       |
|                                                                       |
| uint256 vaultId,                                                      |
|                                                                       |
| address referrer,                                                     |
|                                                                       |
| uint16 rewardChain                                                    |
|                                                                       |
| ) external payable;                                                   |
|                                                                       |
| // Request withdrawal back to this chain                              |
|                                                                       |
| // Called by user; signals BSC Hub to initiate withdrawal             |
|                                                                       |
| function requestWithdrawal(                                           |
|                                                                       |
| uint256 vaultId,                                                      |
|                                                                       |
| uint256 loopAmount,                                                   |
|                                                                       |
| bool earlyExit                                                        |
|                                                                       |
| ) external;                                                           |
|                                                                       |
| // Called by LayerZero endpoint when BSC sends withdrawal             |
|                                                                       |
| // Only callable by trusted LayerZero endpoint                        |
|                                                                       |
| function receiveWithdrawal(                                           |
|                                                                       |
| address recipient,                                                    |
|                                                                       |
| uint256 usdtAmount                                                    |
|                                                                       |
| ) external;                                                           |
|                                                                       |
| }                                                                     |
+-----------------------------------------------------------------------+

**A.4 BSC Hub Vault: Cross-Chain Deposit Handler**

+-----------------------------------------------------------------------+
| // Called by LayerZero endpoint when Stargate delivers bridged USDT   |
|                                                                       |
| // Only callable by trusted Stargate router address                   |
|                                                                       |
| function sgReceive(                                                   |
|                                                                       |
| uint16 srcChainId,                                                    |
|                                                                       |
| bytes memory srcAddress,                                              |
|                                                                       |
| uint256 nonce,                                                        |
|                                                                       |
| address token, // must be BSC USDT                                    |
|                                                                       |
| uint256 amountLD, // USDT amount received                             |
|                                                                       |
| bytes memory payload // ABI-encoded: (address user, uint256 vaultId,  |
| address referrer, uint16 rewardChain)                                 |
|                                                                       |
| ) external onlySgRouter {                                             |
|                                                                       |
| (address user, uint256 vaultId, address referrer, uint16 rewardChain) |
| = abi.decode(payload, \...);                                          |
|                                                                       |
| // Register vault position for user                                   |
|                                                                       |
| // Register referral if referrer != address(0) and no existing        |
| referral                                                              |
|                                                                       |
| // Prompt friction pool funding (emits event for UI)                  |
|                                                                       |
| // Log: srcChainId, user, amount, vaultId                             |
|                                                                       |
| }                                                                     |
+-----------------------------------------------------------------------+

**A.5 wLOOP Bridge Wrapper**

wLOOP is a bridge-backed 1:1 representation of LOOP on non-BSC chains.
It is NOT a separate token with independent supply --- it is an IOU
redeemable for real LOOP on BSC.

  ------------------ ----------------------------------------------------
  **Property**       **wLOOP Behavior**

  Minting            Triggered when user requests LOOP delivery on
                     non-BSC chain at cycle close

  Backing            1:1 locked LOOP on BSC --- held in bridge escrow
                     contract

  Redemption         User bridges wLOOP back to BSC → escrow releases
                     LOOP → standard redemption

  Floor price        Always tracks BSC LOOP floor --- no independent
  tracking           price

  Transferability    Same as LOOP --- non-transferable (bridge back to
                     BSC is the only movement)

  UI display         Shown as \'LOOP\' --- wLOOP implementation is
                     invisible to the user
  ------------------ ----------------------------------------------------

**A.6 Reward Delivery Flow --- Technical**

+-----------------------------------------------------------------------+
| // At cycle close, Hub Vault processes reward delivery per user       |
| preference                                                            |
|                                                                       |
| function deliverReward(                                               |
|                                                                       |
| address user,                                                         |
|                                                                       |
| uint256 usdtAmount, // if user chose USDT                             |
|                                                                       |
| uint256 loopAmount, // if user chose LOOP                             |
|                                                                       |
| uint16 destinationChain // 0 = BSC, else spoke chain ID               |
|                                                                       |
| ) internal {                                                          |
|                                                                       |
| if (destinationChain == BSC_CHAIN_ID) {                               |
|                                                                       |
| // Direct delivery on BSC --- no bridge                               |
|                                                                       |
| if (loopAmount \> 0) \_mintLoop(user, loopAmount);                    |
|                                                                       |
| if (usdtAmount \> 0) \_transferUsdt(user, usdtAmount);                |
|                                                                       |
| } else {                                                              |
|                                                                       |
| // Bridge delivery --- fund from Friction Pool for bridge fees        |
|                                                                       |
| uint256 bridgeFee = \_estimateBridgeFee(destinationChain,             |
| usdtAmount + loopAmount);                                             |
|                                                                       |
| \_deductFromFrictionPool(user, bridgeFee);                            |
|                                                                       |
| if (loopAmount \> 0) {                                                |
|                                                                       |
| \_mintLoop(address(escrow), loopAmount); // mint to escrow            |
|                                                                       |
| \_bridgeWLoop(user, loopAmount, destinationChain); // send wLOOP      |
|                                                                       |
| }                                                                     |
|                                                                       |
| if (usdtAmount \> 0) {                                                |
|                                                                       |
| \_stargateSwap(user, usdtAmount, destinationChain);                   |
|                                                                       |
| }                                                                     |
|                                                                       |
| }                                                                     |
|                                                                       |
| }                                                                     |
+-----------------------------------------------------------------------+

**A.7 Chain ID Reference**

  ------------- ---------- ------------ --------------------------------------------
  **Chain**     **EVM      **Stargate   **USDT Contract**
                Chain ID** Chain ID**   

  BNB Chain     56         102          0x55d398326f99059fF775485246999027B3197955
  (BSC)                                 

  Base          8453       184          0xfde4C96c8593536E31F229EA8f37b2ADa2699bb2

  Arbitrum      42161      110          0xFd086bC7CD5C481DCC9C85ebE478A1C0b69FCbb9

  Polygon       137        109          0xc2132D05D31c914a87C6611C10748AEb04B58e8F

  Ethereum      1          101          0xdAC17F958D2ee523a2206206994597C13D831ec7
  ------------- ---------- ------------ --------------------------------------------

**B Referral Module --- Technical Specification**

**B.1 Non-Negotiables (Invariant-Safe)**

The Referral Program MUST NOT access Vault AUM, Redemption_pool_E,
Ratchet_pool_E, or Friction Pool funds. It must not create protocol
debt, affect Coverage_ratio_E or Redemption_price_E, or mint LOOP.
Referral rewards are funded ONLY from a dedicated Referral Pool (40% of
the marketing allocation).

**B.2 Architecture: Hybrid On-Chain / Off-Chain**

  ------------------ ------------------ ----------------------------------
  **Component**      **On-Chain**       **Off-Chain**

  Referral           Yes --- immutable  Read only
  relationships      once set           

  Reward             Yes --- enforced   Triggers on-chain call
  qualification      at cycle close     

  Referral pool      Yes --- public     Monitored
  balance            state              

  Credit ledger      Yes --- internal   Read only
                     accounting unit    

  Abuse detection    Basic only         Full heuristics
                     (self-ref,         
                     duplicate)         

  Queue processing   Status management  Bot triggers issuance

  Events /           All state changes  Indexed for UI
  transparency       emitted            
  ------------------ ------------------ ----------------------------------

**B.3 Parameters (Governance-Configurable Within Bounds)**

  ----------------------------------- ---------------- ------------------------------
  **Parameter**                       **Default**      **Hard Bounds**

  CREDIT_PER_SIDE                     5 USDT (5e6)     3--10 USDT

  TOTAL_CREDIT_PER_REFERRAL           10 USDT (10e6)   CREDIT_PER_SIDE × 2

  REFERRAL_POOL_MARKETING_SHARE_BPS   4000 (40%)       Off-chain accounting

  CREDIT_LOCK_CYCLES                  1                1--3

  REFERRAL_EXPIRY_DAYS                30               Governance configurable

  MIN_NET_PROFIT_FOR_QUAL             0 (\>= 0         Cannot be negative
                                      qualifies)       

  REQUIRES_ZERO_FRICTION_DEBT         true             Hard requirement
  ----------------------------------- ---------------- ------------------------------

**B.4 On-Chain State**

+-----------------------------------------------------------------------+
| // Referral Relationships                                             |
|                                                                       |
| mapping(address =\> address) public referrerOf; // referred →         |
| referrer (set once)                                                   |
|                                                                       |
| mapping(address =\> uint256) public referralCreatedAt; // timestamp   |
|                                                                       |
| // Referral Status                                                    |
|                                                                       |
| enum ReferralStatus { NONE, PENDING, QUALIFIED, PAID, EXPIRED, VOIDED |
| }                                                                     |
|                                                                       |
| mapping(address =\> ReferralStatus) public referralStatus;            |
|                                                                       |
| mapping(address =\> bool) public rewardIssued; // redundant safety    |
|                                                                       |
| // Credit Ledger (non-ERC20 --- accounting only)                      |
|                                                                       |
| mapping(address =\> uint256) public creditLocked;                     |
|                                                                       |
| mapping(address =\> uint256) public creditUnlocked;                   |
|                                                                       |
| mapping(address =\> uint256) public creditUnlockCycle;                |
|                                                                       |
| // Referral Pool                                                      |
|                                                                       |
| uint256 public referralPoolBalance; // USDT units                     |
|                                                                       |
| // Cross-Chain: referral registration can come from any spoke chain   |
|                                                                       |
| // Passed via Stargate payload and registered on BSC Hub              |
+-----------------------------------------------------------------------+

**B.5 State Machine**

  ----------- ---------------------- -------------------------------------
  **State**   **Description**        **Transitions To**

  NONE        No referral            → PENDING (on setReferrer)
              relationship set       

  PENDING     Relationship set,      → QUALIFIED, EXPIRED, VOIDED
              awaiting deposit +     
              cycle                  

  QUALIFIED   Passed all rules at    → PAID (if pool available), or stays
              cycle close            QUALIFIED (queued)

  PAID        Credits issued to both Terminal
              parties                

  EXPIRED     Did not deposit within Terminal
              expiry window          

  VOIDED      Abuse, rule violation, Terminal
              or Cycle 1 net profit  
              \< 0                   
  ----------- ---------------------- -------------------------------------

**B.6 Qualification Rules (evaluateReferral)**

Called at cycle close by automation bot / keeper / anyone. All checks
performed on-chain:

-   referred has a vault

-   referred made qualifying deposit within expiry window

-   cycleId == referred first completed cycle (Cycle 1)

-   netProfitCycle1 \>= 0 (negative profit → VOIDED, no retry)

-   frictionDebt == 0 if REQUIRES_ZERO_FRICTION_DEBT

-   no early exit during Cycle 1

-   referralStatus == PENDING

-   not voided / not expired

**B.7 Issuance Logic**

+-----------------------------------------------------------------------+
| function issueReferralReward(address referred, uint256 cycleId)       |
| external {                                                            |
|                                                                       |
| require(referralStatus\[referred\] == ReferralStatus.QUALIFIED);      |
|                                                                       |
| require(referralPoolBalance \>= TOTAL_CREDIT_PER_REFERRAL);           |
|                                                                       |
| require(!rewardIssued\[referred\]);                                   |
|                                                                       |
| address referrer = referrerOf\[referred\];                            |
|                                                                       |
| referralPoolBalance -= TOTAL_CREDIT_PER_REFERRAL;                     |
|                                                                       |
| // Lock credits for both parties                                      |
|                                                                       |
| creditLocked\[referrer\] += CREDIT_PER_SIDE;                          |
|                                                                       |
| creditLocked\[referred\] += CREDIT_PER_SIDE;                          |
|                                                                       |
| creditUnlockCycle\[referrer\] = cycleId + CREDIT_LOCK_CYCLES;         |
|                                                                       |
| creditUnlockCycle\[referred\] = cycleId + CREDIT_LOCK_CYCLES;         |
|                                                                       |
| referralStatus\[referred\] = ReferralStatus.PAID;                     |
|                                                                       |
| rewardIssued\[referred\] = true;                                      |
|                                                                       |
| emit ReferralPaid(referred, referrer, cycleId, CREDIT_PER_SIDE);      |
|                                                                       |
| }                                                                     |
+-----------------------------------------------------------------------+

**B.8 Required Events**

-   ReferralLinked(address indexed referred, address indexed referrer,
    uint256 timestamp)

-   ReferralQualified(address indexed referred, address indexed
    referrer, uint256 cycleId)

-   ReferralPaid(address indexed referred, address indexed referrer,
    uint256 cycleId, uint256 creditPerSide)

-   ReferralExpired(address indexed referred, address indexed referrer)

-   ReferralVoided(address indexed referred, address indexed referrer,
    uint8 reasonCode)

-   CreditsUnlocked(address indexed addr, uint256 amount, uint256
    cycleId)

-   ReferralPoolFunded(uint256 amount, uint256 newBalance)

**B.9 Forfeiture Rules**

Credits are forfeited if: (1) user attempts early exit during the credit
lock period (pre-unlock), (2) vault closes before unlock, (3) abuse
detection voids referral before payout. Recommended implementation:
track credits by tranche (CreditTranche { amount, unlockCycle }\[\]) to
avoid wiping unrelated credits.

**B.10 Abuse Controls**

On-chain hard checks: referrer != referred; referrerOf\[referred\] set
once; 1 reward per referred lifetime. Off-chain detection (recommended):
device fingerprint + behavior clustering, suspicious wallet graph
patterns, deposit/withdraw timing patterns. Enforcement: multisig or
governance role calls voidReferral(referred, reasonCode) before payout.
Credits cannot be clawed back after unlock.

**B.11 Testing Checklist**

-   Self referral rejected

-   Duplicate referrer set rejected

-   Referred deposits after expiry =\> EXPIRED

-   Cycle 1 profit negative =\> VOIDED (no retry)

-   frictionDebt \> 0 =\> not qualified

-   Pool insufficient =\> QUALIFIED stays queued

-   Unlock occurs exactly at unlockCycle

-   Credit cannot be withdrawn as USDT

-   Credit applied increases AUM sizing next cycle

-   No interactions with ratchet/redemption/coverage pools

-   Cross-chain referral: referrer address passed via bridge message,
    registered correctly on BSC

**C AI Architecture**

**C.1 AI Philosophy & Scope**

YieldLoop AI is a bounded strategy optimization layer. It does NOT
predict markets with certainty, override protocol invariants, access
user funds directly, control minting logic, or control redemption logic.
It operates entirely off-chain and generates parameter proposals only.
Smart contracts enforce all invariants regardless of AI output.

**C.2 AI Funding Model**

All AI compute is classified as operational infrastructure and funded
exclusively by the Friction Pool --- the same classification as gas
fees, DEX swap fees, and cross-chain bridge fees. AI cost is never
funded from Vault AUM, user principal, performance fee allocation,
ratchet pool, or redemption pool.

**C.3 Model Selection**

  ------------- ---------------------- ----------------------------------
  **Role**      **Model**              **Notes**

  Primary       OpenAI GPT-4o-mini     Cost-efficient, reliable,
                                       auditable outputs

  Fallback      GPT-4o-mini (separate  Automatic failover
                API key)               

  Final         Deterministic safe     No AI dependency --- always
  fallback      preset rules engine    available
  ------------- ---------------------- ----------------------------------

No custom training. No multi-model ensembles. No reinforcement learning
loops. No embeddings. No vector databases. Keep the stack simple and
auditable.

**C.4 Structured Input (JSON Only)**

+-----------------------------------------------------------------------+
| {                                                                     |
|                                                                       |
| \"vault_aum\": 50000,                                                 |
|                                                                       |
| \"volatility_index\": 0.18,                                           |
|                                                                       |
| \"liquidity_depth_bps\": 120,                                         |
|                                                                       |
| \"slippage_estimate_bps\": 8,                                         |
|                                                                       |
| \"trading_performance_30d\": 0.024,                                   |
|                                                                       |
| \"lp_apr_current\": 0.14,                                             |
|                                                                       |
| \"il_estimate_percent\": 0.6,                                         |
|                                                                       |
| \"stablecoin_vault_apy\": 0.052,                                      |
|                                                                       |
| \"bnb_staking_apr\": 0.038,                                           |
|                                                                       |
| \"friction_pool_balance\": 42.50,                                     |
|                                                                       |
| \"friction_debt_status\": 0,                                          |
|                                                                       |
| \"governance_risk_cap_percent\": 3,                                   |
|                                                                       |
| \"current_allocations\": {                                            |
|                                                                       |
| \"trading\": 30, \"lp\": 30, \"stablecoin\": 20, \"bnb_staking\": 20  |
|                                                                       |
| }                                                                     |
|                                                                       |
| }                                                                     |
+-----------------------------------------------------------------------+

**C.5 Structured Output (JSON Only)**

+-----------------------------------------------------------------------+
| {                                                                     |
|                                                                       |
| \"trading_allocation_percent\": 30,                                   |
|                                                                       |
| \"lp_allocation_percent\": 30,                                        |
|                                                                       |
| \"stablecoin_allocation_percent\": 20,                                |
|                                                                       |
| \"bnb_staking_percent\": 20,                                          |
|                                                                       |
| \"lp_range_width_percent\": 8,                                        |
|                                                                       |
| \"risk_cap_percent\": 3,                                              |
|                                                                       |
| \"pause_recommended\": false,                                         |
|                                                                       |
| \"friction_idle_split\": {                                            |
|                                                                       |
| \"stablecoin\": 80,                                                   |
|                                                                       |
| \"bnb_staking\": 20                                                   |
|                                                                       |
| }                                                                     |
|                                                                       |
| }                                                                     |
+-----------------------------------------------------------------------+

Backend validates before applying. If invalid → discard → apply Safe
Preset.

**C.6 Deterministic Safety Layer**

Before any AI output is applied, the backend validator enforces:

-   Allocation sum ≤ 100%

-   Each allocation within governance cap

-   LP ≤ 40%

-   Stablecoin ≤ 30%

-   BNB staking ≤ 80% of BNB

-   Risk cap within governance range

-   Friction idle deployment respects 7-day liquidity rule

+-----------------------------------------------------------------------+
| Safe Preset (applied on AI failure or validation failure):            |
|                                                                       |
| Trading 25% \| LP 25% \| Stablecoin 25% \| BNB Staking 25%            |
|                                                                       |
| LP width: 10% \| Risk cap: 2% \| Friction idle: 100% stablecoin       |
|                                                                       |
| AI failure cannot compromise vault safety --- the safe preset is      |
| always available.                                                     |
+-----------------------------------------------------------------------+

**C.7 Call Frequency & Cost Control**

  ----------------------------------- -----------------------------------
  **Trigger**                         **Frequency**

  Vault creation                      Once per vault

  Cycle start                         Once per cycle

  Daily monitoring (optional)         1x per day if enabled

  Abnormal volatility trigger         Event-driven --- not scheduled
  ----------------------------------- -----------------------------------

Estimated usage: 5--20 calls per vault per month. Estimated monthly AI
cost per vault: under \$1 under normal operation. Easily covered by
Friction Pool sizing model.

**C.8 Logging & Transparency**

Every AI decision logs: timestamp, input snapshot hash, output JSON,
bound validation result, override indicator. User dashboard displays
simplified summary (e.g., \'AI adjusted LP range to 8% due to
volatility\'). Transparency is mandatory.

**D Smart Contract Specification Summary**

**D.1 Contract Inventory**

  -------------------- --------------- -------------------------------------
  **Contract**         **Chain**       **Purpose**

  HubVault.sol         BSC             Core vault: AUM, epoch accounting,
                                       LOOP minting, ratchet, redemption

  FrictionPool.sol     BSC             Per-vault operational reserve ---
                                       segregated from AUM

  ReferralModule.sol   BSC             Referral lifecycle, credit ledger,
                                       pool management

  SpokeGateway.sol     Base / Arb /    Deposit ingress, withdrawal egress,
                       Poly / ETH      bridge interface

  wLOOPBridge.sol      Base / Arb /    wLOOP mint/burn/bridge wrapper
                       Poly / ETH      

  AIValidator.sol      BSC             Off-chain AI output validation before
                                       application
  -------------------- --------------- -------------------------------------

**D.2 Revert / Fail-Closed Behavior**

Transactions MUST revert if:

-   Referral pool insufficient for full reward

-   Referral already paid

-   Referred has no referrer set

-   Status not correct for function

-   Attempts to change referrer after set

-   Coverage_ratio_E would drop below 1.0 post-execution

-   Friction debt exceeds 5% of AUM

-   AI output fails validation (apply safe preset, do not revert vault
    operation)

-   Bridge message sender is not trusted Stargate router

**D.3 Audit Requirements**

-   All standard module audits

-   Friction pool accounting and segregation from AUM

-   Borrow priority logic and debt cap enforcement

-   Friction pool yield exclusion from performance fee

-   Referral module: no interaction with core vault pools

-   Cross-chain bridge message validation (trusted sender, message
    integrity)

-   wLOOP 1:1 escrow integrity --- supply of wLOOP always ≤ escrow LOOP
    balance

-   LOOP canonical chain enforcement --- minting on BSC only

-   SpokeGateway can only receive from trusted Stargate endpoint

**E Capital Allocation & Income Module Specs**

**E.1 Capital Tier Allocation Model**

  ---------- ---------------- --------------- ---------------------------------
  **Tier**   **Source**       **Allocation    **Priority**
                              Range**         

  Reserve    Liquid buffer    Min 25%         1st

  1          Active Trading   25--40%         2nd

  2          Liquidity        Up to 40%       3rd
             Provision                        

  3          Stablecoin       Up to 30%       4th
             Vaults                           

  4          BNB Liquid       Up to 80% of    4th (BNB-specific)
             Staking          BNB             
  ---------- ---------------- --------------- ---------------------------------

**E.2 Liquidity Provision Module**

  --------------------- ------------------------------ -------------------
  **Platform / Pair**   **Notes**                      **Max % of Vault**

  PancakeSwap v3 ---    Stable pair, minimal IL,       20%
  USDT/USDC             concentrated liquidity         

  PancakeSwap v3 ---    High volume, moderate          20%
  USDT/BNB              volatility, strong fees        

  PancakeSwap v3 ---    Correlated pair, reduced       20%
  ETH/BNB               directional risk               

  Thena --- USDT/USDC   ve(3,3) stable pair            20%

  Thena --- USDT/BNB    Competitive APR                20%
  --------------------- ------------------------------ -------------------

Maximum LP as % of vault: 40%. Maximum in any single pair: 20%. Maximum
in any platform: 30%. LP ranges: ±5--15% around current price. AI
proposes ranges at cycle open only. IL calculated explicitly at cycle
close. Friction pool capital: NEVER deployed to LP.

**E.3 Stablecoin Yield Vault Module**

  --------------------- ------------------------------ -------------------
  **Platform**          **Type**                       **Max % of Vault**

  Beefy Finance ---     Auto-compounding optimizer,    20%
  USDT                  instant withdrawal             

  Beefy Finance ---     Same architecture, USDC        20%
  USDC                  denomination                   

  Alpaca Finance ---    Conservative lending pool,     20%
  USDT Savings          overcollateralized only        
  --------------------- ------------------------------ -------------------

**E.4 BNB Liquid Staking Module**

  --------------- ---------------------- ---------------------------------
  **Protocol**    **LST Token**          **Max % of BNB Held**

  Ankr            aBNBc --- liquid,      50% of BNB staking allocation
                  auto-compounding       

  pStake Finance  stkBNB --- BNB Chain   50% of BNB staking allocation
                  native, audited        
  --------------- ---------------------- ---------------------------------

Maximum BNB in liquid staking: 80% of BNB held. Maximum in any single
protocol: 50% of BNB staking. Expected APR: 2--5% (0.16--0.42%/month).
Most consistent income source --- structurally independent of market
conditions. Secondary deployment target for idle Friction Pool capital,
subject to instant DEX liquidity verification.

**F Monthly Accounting & Execution Timing**

  ------------------------------- ---------------------------------------
  **Event**                       **Timing**

  Cycle Start + All Income        1st day @ 00:00 UTC (after strategy
  Sources Open                    lock)

  Friction Pool Idle Deployment   1st day (after 7-day buffer verified)

  Cycle End + All Positions Close Last day @ 23:59 UTC

  Friction Pool Idle Positions    Immediately after cycle end
  Withdrawn (priority)            

  Settlement + Friction Debt      Immediately after cycle end
  Interest Accrual                

  Friction Debt Service (before   1st of next month
  any LOOP mint)                  

  LOOP Mint Event                 1st of next month (after debt service)

  Tiered Ratchet Evaluation       After LOOP mint, before redemption
                                  window

  Redemption Window               After ratchet evaluation, configurable
                                  duration
  ------------------------------- ---------------------------------------

**F.1 Ratchet Evaluation Logic**

+-----------------------------------------------------------------------+
| 1\. Compute Coverage_ratio_E = Redemption_pool_E / (LOOP_supply_E \*  |
| Redemption_price_E)                                                   |
|                                                                       |
| 2\. Identify tier:                                                    |
|                                                                       |
| \< 1.02 → tier 0 (pause)                                              |
|                                                                       |
| 1.02--1.04 → tier 1 (1 tick)                                          |
|                                                                       |
| 1.05--1.09 → tier 2 (4 ticks)                                         |
|                                                                       |
| \>= 1.10 → tier 3 (10 ticks)                                          |
|                                                                       |
| 3\. Required_transfer = Ticks × 0.000025 × LOOP_supply_E              |
|                                                                       |
| 4\. Verify Ratchet_pool_E \>= Required_transfer                       |
|                                                                       |
| 5\. Verify post-transfer Coverage_ratio_E \>= 1.02                    |
|                                                                       |
| 6\. Pass: transfer, update price, log on-chain                        |
|                                                                       |
| 7\. Fail either check: step down one tier, retry                      |
|                                                                       |
| 8\. Tier 1 also fails: ratchet pauses, reason logged                  |
|                                                                       |
| Max ticks per monthly evaluation: 20 (= 0.000500 USDT cap)            |
+-----------------------------------------------------------------------+
