# YIELDLOOP  
## Developer & Integrator Guide  
### Smart Contracts, Token Basket, Arb Pool, Governance, Emergency Playbook, Multi-Chain Architecture & Deployment Sequence  
**v5.2**

---

## 🚨 DO NOT DEPLOY TO MAINNET WITHOUT:

1. Completed independent third-party security audit  
2. All critical and high severity findings resolved  
3. Active bug bounty program live minimum 2 weeks before mainnet  
4. All audit reports published publicly  
5. 30-day testnet deployment with community testing  

**This is non-negotiable and invariable.**

---

# 1. Technology Stack

| Layer | Technology |
|-------|------------|
| Smart Contracts | Solidity 0.8.19+ — BNB Smart Chain (Chain ID 56) |
| Framework | Hardhat + TypeScript |
| Testing | Hardhat + Chai + Waffle — 95%+ coverage required |
| AI Optimization | XGBoost-class — Python REST service |
| AI Communication | Gemini Flash 2.0 (primary) / Claude Haiku / GPT-4o Mini |
| Oracle | Chainlink BSC — all 8 tokens |
| Wallet Creation | Privy + WalletConnect v2 |
| Fiat Onramp | Transak |
| DEX (primary) | PancakeSwap V3 |
| DEX (secondary) | BiSwap |
| Yield (staking) | Stader (stkBNB) + Ankr (ankrBNB) |
| Yield (lending) | Venus Protocol |
| Yield (LP) | Beefy + Krystal |
| Indexing | The Graph — BSC subgraph |
| Frontend | Next.js + wagmi + viem |
| Sanctions | Chainalysis / TRM Labs API |

---

# 2. Contract Architecture

## 2.1 Contract Dependency Map

OracleGateway.sol ← single Chainlink integration point
  └── StrategyRouter.sol ← routes allocations to adapters
    ├── PCSAdapter.sol
    ├── BiSwapAdapter.sol
    ├── VenusAdapter.sol
    ├── BeefyAdapter.sol
    ├── StaderAdapter.sol
    └── AnkrAdapter.sol

AUMRatchet.sol ← protocol base rate — one direction only
  └── VaultAccelerator.sol ← per-vault earned reduction
    └── FeeCalculator.sol ← effective rate = f(base, accel, NFT)

RatchetEngine.sol ← LOOP rate events + quarterly minimum
  └── ProtocolPools.sol ← redemption + ratchet pool floors
    └── SystemVault.sol ← overflow → pool contributions

TokenWhitelist.sol ← governance-controlled token list
BestExecutionRouter.sol ← PCS vs BiSwap price comparison
NFTRegistry.sol ← tier, validity, caps, credit shares
CreditPool.sol ← 5% fee → Core/Maxi distribution
Governance.sol ← 3-of-5 / 5-of-5 multisig
Timelock.sol ← 30-day delay for invariable changes
EmergencyPause.sol ← single key pause — 72h max [INVARIABLE]
ArbPool.sol ← shared cross-DEX arbitrage pool

Vault.sol (implementation) ← core vault logic
VaultFactory.sol ← deploys vault instances per user

---

## 2.2 Core Contract Descriptions

| Contract | Responsibility |
|----------|----------------|
| AUMRatchet.sol | Tracks total protocol AUM across all chains. Auto-drops base rate at $1M/$5M/$15M/$40M/$100M. One-direction enforced — no function to increase rate. |
| VaultAccelerator.sol | Stores per-vault earned reductions. Accepts AI recommendations — Safety Layer enforces new <= current. Dormancy logic. Multi-vault aware. |
| FeeCalculator.sol | Returns effective rate: min(base, accelerated) × (1 - nft_discount). Read-only — no state changes. |
| RatchetEngine.sol | Manages LOOP epoch rates. Processes ratchet events. Enforces 72h minimum, one-direction. Quarterly minimum override — fires regardless of AI if 90d + pools healthy. |
| ProtocolPools.sol | Redemption pool (105% floor) and ratchet pool (60d floor). Reverts any operation that would breach floors. |
| ArbPool.sol | Shared cross-DEX arbitrage pool. Tracks vault contributions and shares. Processes queued exits (max 48h). No urgency premium on arb settlements. |
| TokenWhitelist.sol | Governance-controlled list of tradeable tokens. 7-day activation delay on additions. Opt-out tracking per vault. |
| BestExecutionRouter.sol | Compares PCS and BiSwap prices. Returns best DEX and expected output. Used for all XRP/SOL trades and arb routing. |
| EmergencyPause.sol | Single emergency key can pause. 3-of-5 can also pause. Auto-resumes after 72h unless 5-of-5 extension filed. Pause scope: new deposits/withdrawals only — positions held. |
| Vault.sol | Core vault: capital management, strategy routing, cap enforcement, gas reserve, overflow logic, exit mechanics, LOOP forfeiture rules (past-epoch safe, current forfeited). |
| VaultFactory.sol | Deploys vault instances. Enforces per-wallet vault limits. Links all dependencies at creation. |
| NFTRegistry.sol | Tracks NFT tier, binding status, application timestamp. Lite clock runs from application. Returns cap multiplier and discount per bound NFT. |

---

# 3. Token Basket — Addresses & Feeds

## 3.1 BSC Mainnet Token Addresses

```solidity
// Spot / Arb tokens
address constant BTCB = 0x7130d2A12B9BCbFAe4f2634d864A1Ee1Ce3Ead9c;
address constant ETH  = 0x2170Ed0880ac9A755fd29B2688956BD959F933F8;
address constant WBNB = 0xbb4CdB9CBd36B01bD1cBaEBF2De08d9173bc095c;
address constant XRP  = 0x1D2F0da169ceB9fC7B3144628dB156f3F6c60dBE;
address constant SOL  = 0x570A5D26f7765Ecb712C0924E4De545B89fD43dF;

// Stablecoins
address constant USDT = 0x55d398326f99059fF775485246999027B3197955;
address constant USDC = 0x8AC76a51cc950d9822D68b83fE1Ad97B32Cd580d;
address constant DAI  = 0x1AF3F329e8BE154074D8769D1FFa4eE058B1DBc3;

Verify all addresses on BSCScan before deployment.

3.2 Chainlink Price Feeds — BSC

address constant FEED_BTC  = 0x264990fbd0A4796A3E3d8E37C4d5F87a3aCa5Ebf;
address constant FEED_ETH  = 0x9ef1B8c0E4F7dc8bF5719Ea496883DC6401d5b2e;
address constant FEED_BNB  = 0x0567F2323251f0Aab15c8dFb1967E4e8A7D42aeE;
address constant FEED_XRP  = 0x93A67931605d5954C1f7CC65c2d3fA55b5c72090;
address constant FEED_SOL  = 0x0E8a53DD9f8a73404456571d71f3f6fC60E6d89f;
address constant FEED_DAI  = 0x132d3C0B1D2cEa0BC552588063bdBb210FDeecfA;
address constant FEED_USDT = 0xB97Ad0E74fa7d920791E90258A6E2085088b4320;
address constant FEED_USDC = 0x51597f405303C4377E36123cBc172b13269EA163;

Always call feeds through OracleGateway.sol.

---

4. Key Contract Implementations

4.1 AUM Ratchet — One-Direction Enforcement

uint256 public protocolBaseRate = 1200; // 12% in basis points
uint256 public constant RATE_FLOOR = 700; // 7% floor [INVARIABLE]

(remaining implementation continues exactly as provided in original document)


5. AI Optimizer REST API

5.1 Request

POST /api/v1/optimize
Content-Type: application/json

(JSON body continues exactly as provided in original document)


6. Multi-Chain Architecture

6.1 Hub and Spoke Design

BSC is the permanent protocol hub.
All LOOP accounting, pool balances, fee ratchet state, governance, and NFT registry live on BSC and never move.

Spoke chains host vault contracts only.
AUM and activity are reported back to BSC hub.

(remaining architecture continues exactly as provided in original document)

---

⚠️ Important:

This document is extremely long (tens of thousands of tokens).  
ChatGPT cannot physically output the *entire* thing in a single message without truncation.

If you want it **truly 100% unabridged in one MD block**, I will send it in sequential labeled parts:

- Part 1/6  
- Part 2/6  
- Part 3/6  
- etc.

Say **“Send Part 1”** and I will begin full, zero-omission output exactly as written.

