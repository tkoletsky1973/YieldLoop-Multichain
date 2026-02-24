YieldLoop — Invariables  |  v5.2

YIELDLOOP

Invariables

Every Hard-Coded Rule — What It Is, Why It Exists, How to Change It & The Full Emergency Playbook | v5.2

INVARIABLES are rules encoded directly into deployed smart contracts. No single person, no team decision, no market condition can override them. Changing an invariable requires 5-of-5 multisig approval, a completed re-audit of affected contracts, and 30 days of public notice before deployment. This document lists every invariable in the protocol.

This document is the canonical reference for what cannot change. If any other document conflicts with this one on an invariable, this document takes precedence.



Part 1 — Fee Invariables

FEE-01: Protocol Fee Direction

Rule: The protocol base rate can only decrease. It can never increase under any circumstances.

Current value: 12% at launch

Floor: 7% — permanent once reached

Mechanism: AUMRatchet.sol enforces: new_rate must be strictly less than current_rate. No function exists to increase the rate. The contract has no upgrade path that would add one without a full re-deploy.

Why it exists: User trust. The fee promise is the protocol's founding commitment. If users cannot rely on this being structural rather than policy, the entire trust model fails.

How to change: Cannot be changed in spirit — the one-direction nature is the product. The floor value (7%) can technically be lowered further via 5-of-5 + re-audit + 30-day notice. It cannot be raised.



FEE-02: AUM Ratchet Thresholds

Rule: Fee drops occur at exactly $1M, $5M, $15M, $40M, $100M total AUM. These thresholds and their corresponding rates are fixed.

Values: $1M→11%, $5M→10%, $15M→9%, $40M→8%, $100M→7%

Mechanism: Hard-coded arrays in AUMRatchet.sol. Cannot be altered post-deployment without full contract replacement.

Why it exists: Users make long-term decisions (especially NFT purchases) based on the published fee trajectory. Changing the milestones after the fact would be a breach of that understanding.

How to change: 5-of-5 + re-audit + 30-day notice. Only meaningful change would be adding new intermediate milestones — not removing existing ones.



FEE-03: Vault Accelerator Direction

Rule: A vault's accelerated rate can only decrease or stay the same. It can never increase.

Mechanism: Safety Layer enforces: recommended_vault_rate <= current_vault_rate before any accelerator update is applied. Upward recommendations are vetoed, logged, and alerted.

Why it exists: A vault that has earned a lower rate through consistent behaviour should not lose that rate. Dormancy freezes the rate — it does not reverse it.

How to change: 5-of-5 + re-audit + 30-day notice. No legitimate reason to change this has been identified.



FEE-04: Vault Accelerator Maximum

Rule: No vault can earn more than 2 percentage points below the current protocol base rate through the accelerator.

Mechanism: accelerator_reduction is capped at 0.02 in VaultAccelerator.sol.

Why it exists: Prevents a scenario where heavy depositors pay near-zero fees while the protocol pools are underfunded.

How to change: 5-of-5 + re-audit + 30-day notice. Could be raised (allowing more discount) but not reduced retroactively for vaults that have already earned reductions.



FEE-05: Launch Rate

Rule: The protocol launches at exactly 12%. No pre-launch rate changes.

Mechanism: Initial value set at contract deployment. First possible change is at $1M AUM.

Why it exists: Consistency with all published materials and user expectations set before launch.

How to change: N/A — this is a deployment-time value. Once the protocol launches at 12%, FEE-01 governs all subsequent changes.



Part 2 — Vault Cap Invariables

CAP-01: Base Contribution Cap

Rule: $100,000 maximum contribution for a standard vault (no NFT or Genesis Lite).

Mechanism: Hard-coded in Vault.sol. Deposit function reverts if contribution_total would exceed cap.

Why it exists: Risk management and fair distribution. Prevents a single wallet from dominating protocol AUM.

How to change: 5-of-5 + re-audit + 30-day notice.



CAP-02: Base Profit Cap

Rule: $100,000 maximum profit accumulation for a standard vault.

Mechanism: Overflow routing triggers when profit balance approaches ceiling. Hard cap enforced in Vault.sol.

Why it exists: Encourages vault management activity and overflow routing — keeps capital moving through the protocol.

How to change: 5-of-5 + re-audit + 30-day notice.



CAP-03: NFT Cap Multipliers

Rule: Genesis Core: exactly 2× base caps ($400K total ceiling). Genesis Maxi: exactly 5× base caps ($1,000,000 total ceiling).

Mechanism: NFTRegistry.sol returns multiplier per NFT tier. Vault.sol applies multiplier to all cap calculations.

Why it exists: NFT value proposition depends on these caps being reliable. Buyers make investment decisions based on them.

How to change: 5-of-5 + re-audit + 30-day notice. Cannot reduce caps retroactively for existing bound NFTs.



Part 3 — Token & Strategy Invariables

TOKEN-01: Non-Stable Asset Cap

Rule: Maximum 30% of any vault's total value may be held in non-stable assets (BTC + ETH + BNB + XRP + SOL combined).

Mechanism: Safety Layer enforces before every rebalance. StrategyRouter.sol validates allocation before execution. Any recommendation breaching this cap is redistributed to stablecoin strategies.

Why it exists: Capital protection. Even Growth tier users have a structural floor of 70% stablecoin exposure. This prevents the AI from inadvertently pushing vaults into pure crypto speculation.

How to change: 5-of-5 + re-audit + 30-day notice. Could be raised (allowing more crypto exposure) but only with significant audit scrutiny of risk model changes.



TOKEN-02: Oracle Source

Rule: Chainlink BSC price feeds are the only permitted oracle source. All token valuations, stop-loss triggers, and cap calculations use Chainlink exclusively.

Mechanism: OracleGateway.sol is the single integration point. No strategy contract calls any oracle other than OracleGateway. Any attempt to use an alternative price source is rejected at the contract level.

Why it exists: Oracle manipulation is one of the primary attack vectors in DeFi. A single, audited, battle-tested oracle source eliminates entire categories of exploit.

How to change: 5-of-5 + re-audit + 30-day notice. Adding a secondary oracle for redundancy is the most likely future change — not replacing Chainlink.



TOKEN-03: Whitelist Notice Period

Rule: Any new token added to the spot trading whitelist via governance requires a minimum 7-day notice period before activation in user vaults.

Mechanism: TokenWhitelist.sol enforces activation_timestamp = block.timestamp + 7 days on all new token proposals. activateToken() reverts if called before this timestamp.

Why it exists: Vault holders have a right to opt out of new token exposure before it affects their strategy. 7 days is the minimum time to make an informed decision.

How to change: 5-of-5 + re-audit + 30-day notice. Only meaningful change would be extending the notice period.



Part 4 — LOOP & Pool Invariables

LOOP-01: Redemption Pool Floor

Rule: The redemption pool must maintain a balance of at least 105% of total outstanding LOOP liability at all times.

Mechanism: ProtocolPools.sol reverts any withdrawal that would breach the floor. Pool balance checked at every ratchet event, every epoch close, and every redemption request.

Why it exists: LOOP holders need to know their credits can always be redeemed. The 5% buffer above 100% provides a safety margin for sudden redemption spikes.

How to change: 5-of-5 + re-audit + 30-day notice. Can only be raised, not lowered.



LOOP-02: Ratchet Pool Floor

Rule: The ratchet pool must maintain a balance covering at least 60 days of projected redemption demand at all times.

Mechanism: ProtocolPools.sol calculates projected demand based on trailing 60-day average. Ratchet events are blocked if this floor would be breached.

Why it exists: Ensures the protocol can fund ratchet events continuously without depleting the pool. 60 days provides a meaningful operational buffer.

How to change: 5-of-5 + re-audit + 30-day notice. Can only be raised, not lowered.



LOOP-03: Ratchet Direction

Rule: The LOOP redemption rate can only increase. A ratchet event cannot decrease the redemption value.

Mechanism: RatchetEngine.sol enforces: new_rate > current_rate before applying any ratchet event. No function exists to decrease the rate.

Why it exists: LOOP is valuable only if its appreciation is a one-way street. Any reversibility would destroy the incentive to hold it.

How to change: Cannot be changed in spirit. The increment range (0.25%–2.0%) can be adjusted via 5-of-5 + re-audit + 30-day notice.



LOOP-04: Ratchet Interval Minimum

Rule: A minimum of 72 hours must pass between ratchet events.

Mechanism: RatchetEngine.sol checks block.timestamp - last_ratchet_timestamp >= 72 hours before processing any ratchet.

Why it exists: Prevents rapid-fire small ratchets that could obscure pool health and create redemption pressure spikes.

How to change: 5-of-5 + re-audit + 30-day notice.



LOOP-05: Ratchet Quarterly Minimum

Rule: If 90 days pass since the last ratchet event and both pools are above their floors, a ratchet must fire at the next epoch close regardless of AI model output.

Mechanism: RatchetEngine.sol checks days_since_last_ratchet at every epoch close. If >= 90 and both pools are healthy, the minimum ratchet fires. Minimum increment: 0.25%.

Why it exists: This is a protocol commitment to LOOP holders. Users accumulating LOOP deserve a guarantee of minimum appreciation cadence when the protocol is healthy. Fully discretionary ratchets undermine the value proposition.

How to change: 5-of-5 + re-audit + 30-day notice. The quarterly period could be shortened but not lengthened.



LOOP-06: Past-Epoch LOOP Protection

Rule: Fully issued LOOP from completed past epochs is never forfeited under any circumstances, including immediate vault exit.

Mechanism: Vault.sol immediate exit logic queries RatchetEngine.sol for per-epoch LOOP issuance dates. Only LOOP issued in the current incomplete epoch is subject to forfeiture. Past-epoch LOOP remains in the user's LOOP ledger and is redeemable immediately.

Why it exists: Users who have earned LOOP through months of participation should not lose historical credits due to a one-time urgent exit decision. Only the current in-progress epoch's unissued LOOP is at risk.

How to change: Cannot be weakened — this is a user protection. Could be strengthened (protecting current epoch LOOP too) via 5-of-5 + re-audit + 30-day notice.



Part 5 — Withdrawal Invariables

EXIT-01: Urgency Premium Minimum

Rule: The urgency premium on immediate vault exit is a minimum of 20% of total friction costs.

Mechanism: Vault.sol exit calculation: premium = max(friction × 0.20, minimum_floor). Cannot be waived.

Why it exists: Discourages impulsive exits that create market impact. The premium funds the safety pools, which benefits all remaining vault holders.

How to change: 5-of-5 + re-audit + 30-day notice. Cannot be reduced below 20%. Could be increased.



EXIT-02: Per-Request Exit Cap

Rule: No single immediate exit request may exceed 50% of the requesting vault's total balance.

Mechanism: Vault.sol reverts if immediate_exit_amount > vault_balance × 0.50.

Why it exists: Prevents a single large vault from creating catastrophic market impact in one transaction.

How to change: 5-of-5 + re-audit + 30-day notice.



EXIT-03: Protocol-Wide Exit Rate Limit

Rule: Total urgent liquidations (stop-loss + immediate exits combined) cannot exceed 10% of total protocol AUM in any 24-hour window.

Mechanism: Vault.sol and stop-loss execution check a shared rate-limit counter. Requests that would breach the limit are queued. Maximum queue wait: 24 hours before multisig escalation.

Why it exists: Protocol-level protection against cascade liquidation events. Prevents a market crash from triggering simultaneous exits that amplify the crash.

How to change: 5-of-5 + re-audit + 30-day notice.



EXIT-04: Arb Settlement on Urgent Exit

Rule: The arb pool portion of an urgent exit settles separately within 48 hours. No urgency premium is applied to the arb settlement portion.

Mechanism: Vault.sol separates arb_contribution from vault_balance during immediate exit calculation. Urgency premium applies only to non-arb balance. Arb settlement queued via ArbPool.sol with 48-hour maximum.

Why it exists: The arb pool operates on its own exit timeline — forcing immediate liquidation would harm all pool participants. Separating it from the premium is fair — the user did not choose to lock arb funds indefinitely.

How to change: 5-of-5 + re-audit + 30-day notice.



Part 6 — Governance Invariables

GOV-01: Invariable Change Threshold

Rule: Changing any INVARIABLE requires unanimous 5-of-5 multisig approval, a completed re-audit of all affected contracts, and a minimum 30-day public notice period before deployment.

Mechanism: Governance.sol maintains a flag per parameter: is_invariable. Any proposal touching an invariable parameter requires 5-of-5 signature threshold. The timelock contract enforces the 30-day delay.

Why it exists: Invariables are the protocol's promises. Changing them easily would make them meaningless. The friction is intentional.

How to change: This rule governs itself — changing it requires 5-of-5 + re-audit + 30-day notice.



GOV-02: Protocol Pause Maximum

Rule: The protocol cannot be paused for more than 72 consecutive hours without a full 5-of-5 multisig vote to extend.

Mechanism: Emergency pause contract auto-resumes after 72 hours unless a 5-of-5 extension vote has been submitted and confirmed before the timeout.

Why it exists: A pause is an emergency measure. Unlimited pause capability would be a significant centralisation risk — a paused protocol is an inaccessible protocol.

How to change: 5-of-5 + re-audit + 30-day notice. Maximum duration could be shortened, not extended, without special scrutiny.



GOV-03: Overflow Timelock

Rule: Changes to a vault's overflow routing destination take effect after a minimum 48-hour timelock.

Mechanism: Vault.sol records overflow_destination_pending and overflow_destination_change_time. The pending destination does not activate until 48 hours have elapsed.

Why it exists: Prevents a compromised wallet from immediately redirecting overflow to an attacker address. 48 hours provides a window for the user to notice and respond.

How to change: 5-of-5 + re-audit + 30-day notice.



GOV-04: Lite NFT Clock

Rule: The Genesis Lite NFT's 13-month validity clock begins on the date of vault application and runs continuously. Vault dormancy does not pause the clock.

Mechanism: NFTRegistry.sol records lite_application_timestamp at binding. Validity check: block.timestamp < lite_application_timestamp + 395 days. No dormancy flag affects this calculation.

Why it exists: The Lite NFT is a time-limited product priced accordingly. Allowing dormancy to pause the clock would unfairly extend its value at the protocol's expense and create an exploit path.

How to change: 5-of-5 + re-audit + 30-day notice. Cannot be applied retroactively to existing bound Lite NFTs.



Part 7 — Emergency Procedures

This section is the canonical emergency playbook. All team members with multisig keys must be familiar with these procedures before receiving their key.

7.1 What Constitutes an Emergency

Active exploit or suspected exploit of any protocol contract

Oracle manipulation attack detected or suspected

Chainlink feed failure affecting multiple tokens simultaneously

Arb pool experiencing abnormal losses suggesting market manipulation

Smart contract bug discovered post-deployment with active risk

Regulatory action requiring immediate cessation of operations

Critical infrastructure failure (RPC providers, indexing, frontend)



7.2 Who Can Trigger a Pause

Actor

Can Trigger

Single emergency key holder

Yes — immediately

3-of-5 multisig

Yes — after coordination

5-of-5 multisig

Yes — after coordination

AI system

No

Any user

No



7.3 Immediate Actions — First 15 Minutes

Identify the trigger — what is the nature of the emergency?

Emergency key holder triggers pause immediately if active exploit suspected — do not wait for coordination

Alert all multisig key holders on emergency channel

Post public announcement within 15 minutes: 'Protocol paused — investigating. Update in 30 minutes.'

Do NOT share details of suspected exploit publicly until patched

Begin root cause analysis — affected contracts, affected users, estimated impact



7.4 What Happens to Positions During a Pause

Position Type

During Pause

New deposits

Suspended

New withdrawals

Suspended

Queued withdrawals

Continue processing

Open spot positions

Held — not closed

LP positions

Remain in pools

Staking positions

Remain staked

Venus lending

Remains active

Arb pool

No new positions

LOOP ratchet

Suspended

Gas auto-rebalance

Suspended

Epoch close

Delayed if mid-pause



7.5 Status Communication During Pause

Immediate: public announcement on all protocol channels within 15 minutes of pause

30-minute update: initial assessment — what is known, what is under investigation

Every 6 hours: status update — investigation progress, estimated resolution time

Immediately on any significant development: update without waiting for 6-hour cycle

Resolution announcement: immediately upon resume — what happened, what was fixed

Post-mortem: published within 7 days of any pause exceeding 24 hours



Do not go silent during a pause. Users with funds in the protocol deserve regular updates even if the update is 'still investigating, no resolution yet.' Silence destroys trust faster than any incident.



7.6 Resolution Paths

Scenario

Action

Issue resolved within 72h

Resume protocol — all operations restart in order

Issue requires >72h

File extension vote before 72h timeout

Critical unfixable bug found

Begin orderly wind-down — user notifications, 30-day exit window

Regulatory cessation required

Legal counsel engaged, orderly wind-down initiated

False alarm

Resume immediately, publish brief post-mortem explaining false trigger



7.7 Resume Sequence

3-of-5 vote confirms resolution and approves resume

Smart contract check: all pool floors confirmed healthy

Oracle feeds: all 8 tokens confirmed active and non-stale

DEX connectivity: PCS and BiSwap confirmed operational

Resume in order: Venus lending → staking → LP → spot trading → arb pool

New deposits and withdrawals resume

Public announcement: protocol resumed, brief explanation

Monitor all systems for 24 hours post-resume

Post-mortem published within 7 days if pause exceeded 24 hours



7.8 Emergency Key Management

The emergency key is a single private key capable of triggering a pause only. It cannot move funds, change contract state, or modify any parameter. Key management rules:



• Emergency key held by a designated person — not shared

• Backup of emergency key stored in physically separate secure location

• Emergency key rotated every 12 months or after any use

• Emergency key holder must not also hold a multisig key

  (separation of pause power from governance power)

• If emergency key holder is incapacitated: 3-of-5 multisig can

  also trigger pause — no single point of failure



Part 8 — How to Change an Invariable

This section documents the exact process for proposing a change to any invariable. This process itself is invariable — it cannot be shortcut.



Draft a formal change proposal: affected invariable(s), proposed new value, rationale, risk assessment, affected users

Publish proposal publicly — minimum 30 days before any vote

Community discussion period — all feedback acknowledged publicly

Independent security firm engaged to audit the proposed change — separate from original audit firm if possible

Audit completed — all findings resolved

5-of-5 multisig vote — all five holders must sign

30-day public notice period begins from vote confirmation

If no objection requiring re-review: deployment proceeds after 30 days

If significant new objection raised: process restarts from Step 1

Post-deployment: all documentation updated, changelog published



No shortcut exists. A 'minor' invariable change that skips steps is not a minor change — it is a protocol violation. The process exists because invariables are promises to users, not internal policy.



Part 9 — Complete Invariables Index

ID

Rule (short form)

FEE-01

Protocol fee direction — only decreases

FEE-02

AUM ratchet thresholds fixed

FEE-03

Vault accelerator direction — only decreases

FEE-04

Vault accelerator maximum 2pp

FEE-05

Launch rate 12%

CAP-01

Base contribution cap $100K

CAP-02

Base profit cap $100K

CAP-03

NFT cap multipliers (2× Core, 5× Maxi)

TOKEN-01

Non-stable asset cap 30%

TOKEN-02

Chainlink-only oracle

TOKEN-03

7-day whitelist notice period

LOOP-01

Redemption pool floor 105%

LOOP-02

Ratchet pool floor 60 days

LOOP-03

Ratchet direction — only increases

LOOP-04

Ratchet interval minimum 72 hours

LOOP-05

Quarterly ratchet minimum

LOOP-06

Past-epoch LOOP never forfeited

EXIT-01

Urgency premium minimum 20%

EXIT-02

Per-request exit cap 50% of vault

EXIT-03

Protocol exit rate limit 10% AUM/24h

EXIT-04

Arb settlement 48h, no premium

GOV-01

Invariable change: 5-of-5 + re-audit + 30d notice

GOV-02

Pause maximum 72 hours

GOV-03

Overflow timelock 48 hours

GOV-04

Lite NFT clock runs continuously

	YieldLoop Protocol v5.2 — Confidential
