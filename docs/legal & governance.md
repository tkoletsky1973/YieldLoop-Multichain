**YIELDLOOP**

**Legal & Governance Addendum v2.0**

*Risk Disclosures, AI Authority Framework, and User Responsibility*

+-----------------------------------------------------------------------+
| This document is an addendum to the YieldLoop Whitepaper v2.0.        |
|                                                                       |
| It contains:                                                          |
|                                                                       |
| Section A: Comprehensive Risk Disclosures & User Acknowledgment       |
|                                                                       |
| Section B: AI Governance Authority & Default Control Framework        |
|                                                                       |
| Section C: Governance, Multisig, and Authority Boundaries             |
|                                                                       |
| Section D: Multi-Chain Specific Risk Disclosures                      |
|                                                                       |
| By creating a vault or interacting with the YieldLoop protocol, users |
| acknowledge                                                           |
|                                                                       |
| all disclosures contained in this document. Participation is          |
| voluntary and fully at-risk.                                          |
+-----------------------------------------------------------------------+

**Section A --- Comprehensive Risk Disclosures & User Acknowledgment**

**A.1 No Yield Promise**

YieldLoop does not promise, guarantee, forecast, or imply any fixed or
minimum return. All income is dependent upon market conditions, trading
execution outcomes, liquidity conditions, impermanent loss behavior,
stablecoin vault performance, BNB staking rewards, smart contract
integrity, and on-chain network conditions.

Zero-profit months are possible. Low-profit months are possible.
Temporary ratchet pauses are possible. Participation is voluntary and
fully at-risk.

**A.2 Smart Contract & Third-Party Protocol Risk**

YieldLoop operates entirely on-chain and interacts with external DeFi
protocols, including but not limited to:

-   PancakeSwap, Thena, Beefy Finance, Alpaca Finance

-   Ankr, pStake

-   Stargate Finance (LayerZero) --- cross-chain bridge layer

-   LayerZero relayer network --- message delivery for cross-chain
    operations

Risks include but are not limited to: smart contract vulnerabilities,
oracle malfunction, governance attacks on external protocols, liquidity
collapse, stablecoin depegging, network congestion, validator
instability, MEV extraction, bridge relayer failure, and cross-chain
message delivery delays or failures.

YieldLoop cannot eliminate third-party protocol risk.

**A.3 Cross-Chain & Bridge Risk (New in v2.0)**

YieldLoop v2.0 introduces multi-chain deposit and withdrawal
capabilities via Stargate Finance (LayerZero). Users depositing from or
receiving rewards on chains other than BSC are exposed to additional
risks:

-   Stargate/LayerZero smart contract vulnerabilities

-   LayerZero oracle and relayer failure --- could delay or block
    cross-chain message delivery

-   Bridge liquidity constraints --- large withdrawals may experience
    delays if bridge liquidity is insufficient

-   Chain-specific risks on spoke chains (Base, Arbitrum, Polygon,
    Ethereum)

-   wLOOP bridge escrow risk --- wLOOP is backed by LOOP held in bridge
    escrow; escrow contract risk applies

-   Gas estimation errors on spoke chains causing failed bridge
    initiation

Cross-chain transactions are not instantaneous. Settlement typically
takes 1--5 minutes but may take longer under adverse conditions.
YieldLoop cannot guarantee bridge transaction finality.

**A.4 Market & Liquidity Risk**

All trading activity carries slippage risk, price impact risk,
volatility risk, liquidity withdrawal risk, impermanent loss, and
directional market exposure. LP positions may produce negative net
income during adverse volatility. Trading may underperform expectations.
YieldLoop enforces expectancy checks and risk caps but cannot eliminate
market uncertainty.

**A.5 Friction Pool Disclosure**

The Friction Pool is an operational reserve. Users acknowledge:

-   Friction debt may occur

-   Friction debt accrues interest at 0.5% per month

-   Friction debt is non-waivable

-   Friction debt is deducted automatically before profit minting or
    withdrawal release

-   Vaults may pause if friction debt exceeds 5% of AUM

-   Bridge fees for cross-chain transactions are funded by the Friction
    Pool and increase its projected monthly spend

**A.6 No Financial Advice**

YieldLoop is software. AI proposals, strategy presets, parameter
defaults, and system recommendations are algorithmic outputs generated
within bounded logic. They are not personalized financial advice and do
not constitute fiduciary guidance. No communication from the protocol,
its AI systems, governance participants, or documentation constitutes
investment advice.

**A.7 AI Guidance & User Responsibility**

YieldLoop uses AI systems to guide capital allocation, strategy presets,
and execution parameters. AI guidance does not transfer responsibility
from the user. Before finalizing any vault configuration, the user must
explicitly acknowledge:

  -----------------------------------------------------------------------
  *\"I understand that although AI is guiding strategy, presets, and
  execution parameters, I remain fully responsible for my participation,
  configuration choices, deposits, and outcomes. I am not relying on
  YieldLoop or its AI as financial advice.\"*

  -----------------------------------------------------------------------

Users acknowledge: AI outputs are probabilistic, not guarantees; AI may
be incorrect; AI may underperform; AI may pause trading conservatively;
AI may recommend allocations that do not generate profit.

**A.8 Regulatory & Jurisdictional Risk**

Users are solely responsible for determining legality of participation
in their jurisdiction, tax reporting, compliance with local regulations,
and reporting digital asset gains or losses. YieldLoop does not assess
jurisdictional compliance for individual participants. Participation is
restricted in any jurisdiction where decentralized financial
infrastructure is prohibited. Multi-chain access does not reduce or
modify jurisdictional obligations.

**A.9 Acknowledgment of Risk**

By creating a vault or interacting with the protocol, the user affirms
all of the following:

  -------- -------------------------------------------------------------------
  **\#**   **Acknowledgment**

  1        They understand digital asset risk

  2        They understand DeFi protocol risk

  3        They accept possible partial or total loss

  4        They understand LOOP is non-transferable and epoch-isolated

  5        They understand ratchet appreciation is surplus-dependent

  6        They understand no invariant guarantees profit

  7        They accept that software may pause under fail-closed conditions

  8        They understand AI guidance does not remove personal responsibility

  9        They understand cross-chain bridge transactions carry third-party
           protocol risk

  10       They understand wLOOP on non-BSC chains is a bridge-backed
           representation, not native LOOP

  11       They are responsible for their own tax and regulatory compliance in
           their jurisdiction
  -------- -------------------------------------------------------------------

**Section B --- AI Governance Authority & Default Control Framework**

**B.1 AI as Primary Operational Controller**

YieldLoop AI systems manage: trade sizing within risk caps, LP range
placement, stablecoin vault allocation, BNB staking allocation, friction
pool idle deployment, strategy parameter presets, risk cap
recommendations, volatility response adjustments, drawdown mitigation
logic, and ratchet evaluation execution timing. AI operates strictly
within hard-coded invariants and governance bounds.

**B.2 AI Controls All Defaults**

All vault creation and configuration screens present AI-generated
default presets. Default state equals AI-selected configuration. User
must actively modify settings to override defaults. This ensures
disciplined participation, reduced emotional misconfiguration,
consistent risk posture, and reduced user error.

**B.3 User Override & Responsibility Logging**

Users may override AI recommendations within governance-approved limits.
If a user modifies risk caps, allocation splits, LP range width,
stablecoin vault weighting, BNB staking ratio, or friction pool target,
the override is logged on-chain or within system records. Before
finalization, user must acknowledge:

  -----------------------------------------------------------------------
  *\"I understand I am modifying AI-recommended parameters and accept
  responsibility for this change.\"*

  -----------------------------------------------------------------------

YieldLoop bears no responsibility for user-driven deviations from AI
defaults.

**B.4 AI Limitation Boundaries**

  ----------------------------------- -----------------------------------
  **AI May**                          **AI May Not**

  Present options and recommend       Guarantee profit or predict markets
  adjustments                         with certainty

  Recommend pause or reduced exposure Override invariants
  conditions                          

  Adjust strategy parameters within   Mint LOOP outside realized profit
  bounds                              

  Propose friction pool idle          Change fee structure
  deployment splits                   

  Recommend volatility-based          Waive friction debt
  throttling                          

  Flag anomalies across all income    Access user funds directly
  sources                             

  Propose cross-chain fee             Approve unauthorized bridge routes
  optimization                        
  ----------------------------------- -----------------------------------

AI outputs are assistive tools, not guarantees of performance. AI model
upgrades require 48-hour public notice before activation and cannot
alter invariants, fee structure, liability logic, or ratchet formula.

**Section C --- Governance, Multisig, and Authority Boundaries**

**C.1 Hard-Coded Invariants (Non-Overrideable)**

The following cannot be modified by AI, governance, multisig, or admin
under any circumstances:

  ------------------------ ----------------------------------------------
  **Invariant**            **Rule**

  Coverage_ratio_E ≥ 1.0   System fails closed before this breaks

  Non-decreasing           Floor never retreats --- ever
  Redemption_price_E       

  Profit-only LOOP minting No emissions, no speculative minting

  Epoch isolation          No cross-epoch subsidy of any kind

  Friction pool            Never counted as AUM, never in coverage
  segregation              

  Friction debt            Cannot be forgiven by any authority
  non-waivable             

  No leverage rule         No borrowing against deposited assets

  No cross-epoch subsidy   Each epoch stands alone

  LOOP canonical on BSC    Single source of truth --- cannot be split
                           across chains
  ------------------------ ----------------------------------------------

These are immutable at the contract level.

**C.2 Limited Multisig Authority**

Multisig is restricted to the following actions only:

-   Emergency pause

-   External protocol whitelist updates

-   Risk cap boundary adjustment (within predefined bounds)

-   Audit upgrade execution

-   AI model upgrade deployment (with 48-hour notice)

-   Platform integration approval

-   SpokeGateway contract updates on spoke chains

-   Authorized bridge protocol updates

Multisig may NOT:

-   Move user funds

-   Access vault AUM

-   Access friction pool capital

-   Override coverage math

-   Adjust redemption price

-   Modify fee splits retroactively

-   Override LOOP canonical chain designation

-   Access wLOOP bridge escrow funds

Multisig authority is operational, not economic.

**C.3 Governance Hierarchy**

  ----------- --------------- ------------------------------- ----------------------
  **Level**   **Authority**   **Scope**                       **Overrideable By**

  1           Hard-coded      Coverage, isolation,            Nobody --- immutable
              invariants      non-decreasing price,           
                              profit-only minting, friction   
                              segregation, LOOP canonical     
                              chain                           

  2           Smart contract  All invariants enforced at      Nobody --- code
              enforcement     contract level                  enforced

  3           Governance      Risk caps, ratchet thresholds,  NFT holders only,
              bounds          friction parameters (within     within bounds
                              guardrails)                     

  4           AI optimization Trade params, LP ranges, vault  Within Level 3 bounds
                              selection, friction idle        only
                              deployment                      

  5           User            Override AI defaults within     User decision ---
              configuration   Level 3 bounds                  logged on-chain
  ----------- --------------- ------------------------------- ----------------------

**C.4 AI Model Upgrade Control**

AI models may be updated through governance-approved deployment.
Requirements: public disclosure, version tagging, change summary,
48-hour notice before activation. AI upgrades cannot alter invariants,
fee structure, liability logic, or ratchet formula. AI controls strategy
execution --- not liability architecture.

**Section D --- Multi-Chain Governance & Authority**

**D.1 Spoke Chain Authority Model**

SpokeGateway contracts on Base, Arbitrum, Polygon, and Ethereum operate
under the following authority model:

  --------------------- --------------- ----------------------------------
  **Action**            **Authority     **Notes**
                        Required**      

  Deploy new            Multisig        Must be whitelisted on BSC Hub
  SpokeGateway                          before accepting deposits

  Update SpokeGateway   Multisig + 48hr Cannot change bridge message
  implementation        notice          format without BSC Hub update

  Pause SpokeGateway on Multisig        Emergency only --- deposits
  a chain                               revert, existing vaults unaffected

  Update trusted bridge Multisig        Stargate router address changes
  endpoint                              require governance approval

  Add new spoke chain   Governance vote Requires SpokeGateway deployment +
                                        BSC Hub whitelist + audit
  --------------------- --------------- ----------------------------------

**D.2 LOOP Canonical Chain Enforcement**

LOOP minting and redemption occur exclusively on BSC. This rule is
enforced at the smart contract level and cannot be modified by multisig,
governance, or AI. Any representation of LOOP on non-BSC chains is wLOOP
--- a bridge-backed IOU backed 1:1 by LOOP held in escrow on BSC.

wLOOP escrow contracts are subject to the same multisig authority model
as SpokeGateway contracts. The escrow invariant --- wLOOP supply ≤ LOOP
in escrow --- is enforced at the contract level and is non-overrideable.

**D.3 Cross-Chain Risk Acknowledgment**

By depositing from a non-BSC chain, users additionally acknowledge:

-   Bridge transactions are irreversible once initiated

-   YieldLoop cannot recover funds lost due to Stargate/LayerZero
    protocol failure

-   Bridge fees funded by the Friction Pool increase friction debt
    exposure for the vault

-   Users selecting reward delivery on non-BSC chains accept additional
    bridge settlement time (typically 1--5 minutes)

-   wLOOP on non-BSC chains is subject to bridge escrow risk independent
    of vault performance

**Authority Model Summary**

+-----------------------------------------------------------------------+
| YieldLoop v2.0 is structured so that:                                 |
|                                                                       |
| **■ Invariants protect solvency --- enforced by code, not promises**  |
|                                                                       |
| **■ AI manages operational intelligence within hard boundaries**      |
|                                                                       |
| **■ Multisig protects emergency governance --- cannot touch user      |
| funds**                                                               |
|                                                                       |
| **■ SpokeGateway contracts provide multi-chain access under unified   |
| rules**                                                               |
|                                                                       |
| **■ LOOP remains canonical on BSC --- single source of truth**        |
|                                                                       |
| **■ wLOOP on other chains is bridge-backed 1:1 --- not independent    |
| supply**                                                              |
|                                                                       |
| **■ Users retain final participation authority --- all overrides      |
| logged**                                                              |
|                                                                       |
| **■ Liability is epoch-isolated --- no cross-epoch contamination**    |
|                                                                       |
| **■ Operational debt is transparent, interest-bearing, and            |
| non-waivable**                                                        |
|                                                                       |
| **■ Bridge risk is disclosed, accepted, and cannot be insured by the  |
| protocol**                                                            |
|                                                                       |
| AI guidance does not transfer risk from the participant to the        |
| protocol.                                                             |
|                                                                       |
| Responsibility for participation, deposits, configuration, and        |
| outcomes remains with the user.                                       |
|                                                                       |
| Discipline is enforced by code.                                       |
+-----------------------------------------------------------------------+

**YieldLoop Legal & Governance Addendum v2.0**

Addendum to Whitepaper v2.0 \| BSC Hub \| Base \| Arbitrum \| Polygon \|
Ethereum

*Todd Koletsky, HT1 (SW), USN (Ret.) --- Systems Architect*
