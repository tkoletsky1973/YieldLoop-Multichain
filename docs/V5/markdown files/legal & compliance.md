YieldLoop — Compliance & Legal  |  v5.2

YIELDLOOP

Compliance & Legal

Disclaimers, Sanctions, Wallet Access Loss, Death & Incapacitation, Tax Reporting, Regulatory Framework & User Responsibilities | v5.2

THIS DOCUMENT DOES NOT CONSTITUTE LEGAL OR FINANCIAL ADVICE. YIELDLOOP LTD. IS NOT A LICENSED FINANCIAL INSTITUTION, INVESTMENT ADVISER, OR BROKER-DEALER IN ANY JURISDICTION. USERS ARE SOLELY RESPONSIBLE FOR DETERMINING WHETHER USE OF THIS PROTOCOL IS LAWFUL IN THEIR JURISDICTION AND FOR MEETING ALL APPLICABLE TAX AND REGULATORY OBLIGATIONS.



1. Entity & Jurisdiction

Legal entity: YieldLoop Ltd.

Jurisdiction of incorporation: Seychelles

Protocol deployment: BNB Smart Chain (Chain ID 56) — a public, permissionless blockchain

Nature of protocol: Non-custodial smart contract infrastructure. YieldLoop Ltd. does not hold, control, or have access to user funds at any time.

LOOP token status: Internal non-transferable accounting unit. Not a cryptocurrency, not a security, not a financial instrument. Has no value outside the YieldLoop protocol.

NFT status: Utility tokens representing protocol access rights and fee discounts. Not securities. Not investment contracts. Buyers purchase functional protocol benefits, not an expectation of profit from the efforts of others.



YieldLoop operates as a protocol, not a financial service provider. The smart contracts are autonomous — they execute based on user instructions and protocol rules. YieldLoop Ltd. maintains and develops the protocol but does not intermediate transactions or hold assets.



2. Not Investment Advice

Nothing in any YieldLoop document, interface, AI conversation, notification, or communication constitutes investment advice, financial advice, tax advice, legal advice, or any other form of professional advice. All content is informational only.

The AI recommendation system produces strategy suggestions based on user-provided goals and market data. These suggestions are not financial advice. Users who follow AI recommendations do so entirely at their own discretion. YieldLoop Ltd. accepts no liability for outcomes resulting from AI recommendations.

Historical yield performance of any strategy, pool, or token mentioned in YieldLoop materials does not indicate, guarantee, or imply future performance. DeFi yields are variable and can go to zero.



3. Risk Disclosures

3.1 Capital Loss Risk

Users may lose all funds deposited in a YieldLoop vault. This is not a theoretical risk — it is a realistic possibility in the following scenarios:

Smart contract exploit: a bug in the protocol contracts could allow an attacker to drain vault funds. The protocol requires an independent audit before launch, but no audit guarantees absence of bugs.

Market risk: severe and sustained crypto market decline can cause vault strategy losses that exceed yield earned. Conservative settings reduce but do not eliminate this risk.

Oracle failure or manipulation: if Chainlink price feeds provide incorrect data, the AI may make trades at wrong prices, triggering incorrect stop-losses or take-profits.

DEX failure: if PancakeSwap or BiSwap experience an exploit or failure, LP positions and pending trades may be affected.

Staking slashing: liquid staking protocols (Stader, Ankr) carry slashing risk. A validator misbehaviour event could reduce stkBNB or ankrBNB value.

Wrapped token depeg: XRP and SOL trade as BEP-20 wrapped tokens. A failure in the Binance bridge or wrapping mechanism could cause the BSC-side token to depeg from its native chain price.

Regulatory action: government action against DeFi protocols, specific tokens, or blockchain infrastructure could render the protocol inaccessible or certain strategies non-functional.

3.2 Smart Contract Risk

YieldLoop smart contracts are published open source. They are required to undergo independent third-party security audit and active bug bounty before mainnet deployment. Audit reports are published publicly. However:

No audit eliminates all risk. Complex DeFi protocols have been exploited after audits.

Upgrades and new integrations introduce new attack surface. Each significant protocol update requires re-audit of affected contracts.

Composability risk: YieldLoop integrates with Venus, Beefy, Stader, Ankr, PancakeSwap, and BiSwap. A vulnerability in any of these protocols could affect YieldLoop vaults.

3.3 Liquidity Risk

DeFi liquidity can change rapidly. LP positions may experience impermanent loss. Staking unbonding periods can prevent immediate exit. Urgent withdrawal mechanics exist to address this but carry costs. Users should understand that not all vault capital is immediately liquid at all times.

3.4 Technology Risk

YieldLoop depends on the BNB Smart Chain network, Chainlink oracle network, and multiple third-party DeFi protocols. Network congestion, chain reorganisations, or infrastructure failures can delay or affect vault operations. The protocol has no control over the underlying blockchain infrastructure.

3.5 AI Model Risk

The AI recommendation system uses machine learning models that can produce suboptimal or incorrect outputs. The Safety Layer enforces invariables but does not guarantee optimal strategy performance. AI models trained on historical data may not perform well in novel market conditions.



4. Sanctions Compliance

4.1 Screening Requirement

YieldLoop performs sanctions screening at every wallet connection. Wallets associated with individuals or entities on the following lists are blocked from protocol access:

OFAC SDN List (United States Office of Foreign Assets Control)

EU Consolidated Sanctions List

UN Security Council Consolidated List

UK Financial Sanctions List (HM Treasury)

Any other list required by applicable law at the time of screening

4.2 Screening Process

Screening occurs at each wallet connection event — not session-cached. If a wallet address appears on a sanctions list, protocol access is denied and the wallet connection is rejected. The user is informed that access is unavailable but not given specific reasons (to avoid tipping off sanctioned actors).

Screening is performed against the wallet address, not the user's identity. YieldLoop is not a KYC provider and does not verify the identity of wallet holders. Users represent and warrant at vault creation that they are not subject to any sanctions.

4.3 User Representation

By creating a vault and completing the acknowledgment process, users explicitly confirm:

They are not on any international sanctions list

They are not acting on behalf of any sanctioned individual or entity

Their funds do not originate from sanctioned sources

Use of the protocol is legal in their jurisdiction



False representations in the vault acknowledgment do not create liability for YieldLoop Ltd. They do create legal liability for the user under applicable sanctions law. Sanctions violations carry severe criminal and civil penalties in most jurisdictions.



5. Jurisdictional Access Restrictions

5.1 Restricted Jurisdictions

Access to YieldLoop is restricted for users in the following jurisdictions. This list is subject to change based on regulatory developments. Users are responsible for checking current restrictions before accessing the protocol.

Jurisdiction

Reason for Restriction

United States of America

SEC regulatory environment — unregistered securities risk, FinCEN requirements

United Kingdom

FCA regulated activities — ongoing regulatory review of DeFi

Canada

Provincial securities law complexity

North Korea (DPRK)

OFAC sanctions — total prohibition

Iran

OFAC sanctions — total prohibition

Cuba

OFAC sanctions — total prohibition

Syria

OFAC sanctions — total prohibition

Russia

EU/UK/US sanctions — significant restrictions

Belarus

EU/UK/US sanctions



This list is not exhaustive. Regulatory environments change. Users in any jurisdiction not listed above must independently verify that protocol use is lawful in their location. YieldLoop Ltd. does not provide legal advice on jurisdictional access.



YieldLoop uses IP-based geoblocking as a technical measure to reduce access from restricted jurisdictions. IP geoblocking is not a guarantee — VPN and proxy use can circumvent it. Technical blocking does not substitute for user responsibility to comply with their local laws.



6. Wallet Access Loss, Death & Incapacitation

6.1 The Non-Custodial Reality

YieldLoop is non-custodial. This is one of its most important features — and one of its most important responsibilities. The protocol has no mechanism to identify who controls a wallet address, verify claims of ownership, or take instructions from anyone other than the controlling private key.

This means that if access to the controlling wallet is lost — for any reason — the vault and all its contents may be permanently inaccessible. There is no customer support escalation path. There is no court order mechanism. There is no estate process that the protocol can honour.

6.2 What YieldLoop Cannot Do

Recover a lost seed phrase, private key, or hardware wallet

Identify or verify the real-world identity of a wallet holder

Transfer vault ownership or control to a named beneficiary

Accept instructions, power of attorney, court orders, or probate documents

Freeze, hold, or preserve a vault pending legal proceedings

Provide access to a vault without the controlling private key or seed phrase

Refund funds from an inaccessible vault



If your seed phrase is lost and you cannot access your wallet, your vault funds may be gone permanently. YieldLoop cannot help. This is not a limitation we can change — it is the fundamental nature of non-custodial blockchain technology.

6.3 Causes of Permanent Access Loss

The following situations can result in permanent loss of vault access. Users should take precautions against each:

Cause

Risk Level

Lost seed phrase

Critical

Device loss or damage (hot wallet)

High

Hardware wallet failure

Medium

Death of wallet holder

High

Incapacitation (medical)

Medium

Forgotten password (Privy/custodial setup)

Medium

Key compromise (hacked)

High

Multi-sig deadlock

Low



6.4 Recommended Precautions

YieldLoop strongly recommends the following practices for any user holding significant vault balances:

Write your seed phrase on paper. Store it in a physically secure location — a safe, safety deposit box, or similar. Do not store it digitally in any form — not in email, notes apps, cloud storage, screenshots, or text messages.

Consider a hardware wallet (Ledger, Trezor, or equivalent) for significant balances. Hardware wallets are resistant to remote compromise and device loss — the seed phrase is the recovery key.

Make multiple physical copies of your seed phrase and store them in different physical locations. A house fire or flood should not be able to destroy your only copy.

For estate planning: prepare a sealed document that explains to a trusted person that a YieldLoop vault exists and where to find the seed phrase or wallet access instructions. This document should be stored with your will or equivalent. Do not give the trusted person direct access to the seed phrase while you are alive unless you have established appropriate legal protections.

Consider a personal multi-signature setup: a 2-of-3 personal multisig where three trusted parties each hold one key means that two of three keys are needed for access. No single person can act alone — but the original holder's incapacitation does not mean permanent loss.

For very large balances: engage a digital asset estate planning specialist. This is an emerging field with real practitioners who can help structure access and inheritance properly.



YieldLoop may in future offer optional vault recovery features such as time-locked recovery addresses or social recovery mechanisms. These would be opt-in and would not change the default non-custodial architecture. Any such features would be clearly documented and audited before availability.

6.5 Acknowledgment at Vault Creation

The vault creation acknowledgment includes a mandatory checkbox that users must individually tick confirming:

[ ] I understand that YieldLoop is non-custodial. If I lose access to

    my wallet — including through device loss, forgotten credentials,

    theft, death, or incapacitation — YieldLoop cannot recover my funds

    or transfer vault control to any other person. I accept full

    responsibility for securing my wallet access credentials.



This checkbox cannot be pre-ticked, cannot be skipped, and the vault cannot be created without it being individually selected.



7. Tax Obligations

7.1 User Responsibility

Users are solely responsible for determining and meeting their tax obligations arising from YieldLoop vault activity in their jurisdiction. Tax treatment of digital asset activities — including yield farming, staking rewards, LP income, trading profits, and token appreciation — varies significantly by country and is subject to ongoing regulatory change.

YieldLoop does not provide tax advice, does not calculate tax obligations, and does not report user activity to any tax authority. The protocol's tax export feature provides raw transaction data to assist users in meeting their own obligations.

7.2 Potentially Taxable Events

The following vault activities may constitute taxable events in some or all jurisdictions. Users should consult a qualified tax professional in their country:

Depositing cryptocurrency into a vault (may trigger disposal event in some jurisdictions)

USDT profit claims — may be treated as income, capital gain, or both depending on jurisdiction

LOOP issuance — may be treated as income at issuance value

LOOP redemption — may trigger capital gain or loss calculation based on issuance vs redemption value

LOOP ratchet appreciation — may or may not be a taxable event depending on jurisdiction

NFT purchases — may trigger capital gain or loss depending on payment method and jurisdiction

Stop-loss and take-profit executions — typically capital gain or loss events

LP income and impermanent loss — treatment varies significantly by jurisdiction

Staking rewards — commonly treated as income in many jurisdictions

Referral and deposit credits — may be treated as income when applied

7.3 Tax Export Feature

YieldLoop provides a universal CSV export of all vault activity. This export is available at any time from the transaction history page and can be filtered by date range. The export contains the minimum data set required to calculate obligations under any country's tax framework.

The CSV includes: date and time (UTC), transaction type, token in and amount, token out and amount, USD value at time of transaction (via Chainlink price), platform fee paid, running vault balance, vault identifier, on-chain transaction hash, and a plain-language description of each event.

This data can be imported into leading crypto tax software platforms and can be provided directly to accountants. It does not constitute a tax return or official tax document — it is raw transaction data.

7.4 Record Keeping

Users should retain their YieldLoop tax export records for the full period required by their jurisdiction's tax law — commonly 5 to 7 years. YieldLoop stores transaction data for as long as the protocol is operational, but users should maintain their own copies.



8. Anti-Money Laundering

YieldLoop implements the following AML-adjacent measures, noting that the protocol is not a regulated financial institution and is not subject to formal AML requirements in most jurisdictions:

Sanctions screening at every wallet connection — as described in Section 4

No fiat custody — YieldLoop never holds fiat currency. The Transak onramp is a separate regulated service.

Non-custodial architecture — user funds move directly between user wallets and smart contracts. YieldLoop Ltd. does not intermediate fund flows.

Public blockchain — all transactions are permanently recorded on BSC and publicly verifiable

Referral anti-sybil measures — prevents obvious self-referral schemes



Users warrant at vault creation that their deposited funds do not originate from unlawful activity. The protocol cannot verify fund origins — this representation is solely the user's responsibility.



9. Privacy

YieldLoop collects minimal data. The protocol is designed to function with wallet addresses only — no real-world identity is required or stored by the protocol itself.

9.1 What the Protocol Stores On-Chain

Wallet addresses and vault parameters — publicly visible on BSC

Transaction history — publicly visible on BSC

Acknowledgment hashes — publicly visible on BSC

LOOP balances and ratchet history — publicly visible on BSC



9.2 What YieldLoop Ltd. Stores Off-Chain

Encrypted AI conversation records — linked to acknowledgment hash, not to real-world identity

Email addresses — if user provides for notifications (optional)

IP address logs — for sanctions screening and rate limiting, retained per applicable law

Referral code relationships — wallet-to-wallet, no real-world identity



9.3 What YieldLoop Does Not Collect

Real name, address, date of birth, or government ID (KYC not required by protocol)

Bank account or payment card details (Transak handles fiat onramp independently)

Device fingerprints beyond what is necessary for sanctions screening



The Transak fiat onramp is operated by Transak and subject to Transak's own privacy policy and KYC requirements. YieldLoop does not receive or store identity data collected by Transak.



10. Intellectual Property

YieldLoop smart contracts are published open source under MIT license. The protocol code may be forked and redeployed. However:

The YieldLoop name, logo, and brand marks are proprietary and may not be used by forks or competing products

AI model weights and training data are proprietary

The document suite (including this document) is proprietary — may not be reproduced for commercial purposes without written permission

Protocol documentation and specifications are published for transparency — not as a licence to replicate the product



11. Limitation of Liability

To the maximum extent permitted by applicable law, YieldLoop Ltd., its directors, officers, employees, contractors, and agents shall not be liable for:

Any loss of funds resulting from smart contract bugs, exploits, or vulnerabilities

Any loss resulting from market movements, strategy underperformance, or AI model errors

Any loss resulting from wallet access loss, death, incapacitation, or key mismanagement

Any loss resulting from third-party protocol failures (Venus, Beefy, Stader, Ankr, PancakeSwap, BiSwap, Chainlink)

Any loss resulting from oracle failures, network outages, or blockchain infrastructure issues

Any indirect, consequential, special, incidental, or punitive damages of any kind

Any regulatory or tax consequences arising from protocol use in any jurisdiction



Total liability of YieldLoop Ltd. to any user shall not exceed the amount of platform fees paid by that user in the 12 months preceding the claim.



12. Governing Law & Dispute Resolution

Governing law: Laws of the Republic of Seychelles

Dispute resolution: Binding arbitration under Seychelles International Arbitration Act

Arbitration seat: Victoria, Mahé, Seychelles

Language: English

Class action waiver: Users waive the right to participate in any class action or consolidated proceeding against YieldLoop Ltd.



Users in some jurisdictions may have statutory rights that cannot be waived by contract. Nothing in this document is intended to exclude statutory rights that cannot be excluded under applicable law.



13. Amendments

YieldLoop Ltd. reserves the right to amend these legal terms at any time. Material changes will be communicated via dashboard notification and email (for users who have opted in) with a minimum 30-day notice period before taking effect. Continued use of the protocol after the effective date of changes constitutes acceptance.

Protocol invariables cannot be changed through this amendment process — changes to invariables require the separate process defined in Doc 05. Legal terms and risk disclosures are separate from protocol invariables.



14. Contact

General enquiries: legal@yieldloop.io

Security disclosures: security@yieldloop.io (use PGP key published on protocol website for sensitive disclosures)

Bug bounty: See protocol website for active bug bounty scope and rewards



YieldLoop Protocol v5.2 — Compliance & Legal — For informational purposes only — Not legal advice — Last reviewed: v5.2

	YieldLoop Protocol v5.2 — Confidential
