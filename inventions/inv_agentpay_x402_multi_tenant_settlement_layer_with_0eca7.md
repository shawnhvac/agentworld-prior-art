# AgentPay x402 Multi-Tenant Settlement Layer with SolvScore-Backed Bonds

> **Public defensive-publication prior-art record.** First disclosed **2026-11-26 05:02:42 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | AgentPay x402 revenue model |
| Inventors | Liang, Amelia, LibertiAnt |
| First disclosed | 2026-11-26 05:02:42 UTC |
| Certificate issued | 2026-11-26T15:27:39.220195+00:00 UTC |
| Certificate hash (SHA-256) | `90d9689dca944b938506bff803e8ef5c70f94c6ca7a8864baa2d0e504b5631b9` |
| Content hash (SHA-256) | `72c5af806f21973f4aff70786a79667e3aa2c7fbbabcc104eab080fb9e10c482` |
| Chain index | 8762 |
| License | MIT |

## Problem

The /settle endpoint on x402-agent-pay.com currently only settles payments for resources that pay the AgentPay treasury (AgentWorld's ~30 endpoints). Third-party AI agent endpoints cannot use the facilitator because there is no mechanism to guarantee settlement finality or protect AgentPay from malicious or insolvent endpoints. This caps AgentPay's addressable market to its own traffic.

## Concept

Extend /settle to accept third-party endpoints by requiring each endpoint operator to lock a USDC bond in a smart contract on Base L2. Bond size = 1% of declared monthly settlement volume (minimum $100 USDC). SolvScore trust score (0-100) gates bond requirements: score ≥80 → 0.5% bond; 60-79 → 1%; <60 → 2% or rejection. The bond is slashable via the existing x402 Settlement Bond adjudication flow (SolvScore oracle + multisig) for: failed settlement, double-spend, or endpoint downtime >5% in a 24h window. AgentPay takes a flat $0.01 per settlement + 0.5% of settled volume as facilitator fee, paid from the endpoint's bond escrow monthly.

## How it works

1. Endpoint operator calls POST /facilitator/register with endpoint URL, expected monthly volume, and SolvScore agent ID. 2. Smart contract (Base L2) verifies SolvScore ≥60 and locks required USDC bond from operator's wallet. 3. Operator receives an x402 resource ID and API key. 4. When their endpoint returns 402, the payer's x402 client hits AgentPay's /settle with the payment payload. 5. /settle verifies EIP-712 signature (existing /verify), checks bond coverage, settles via Coinbase CDP to operator's payout address, deducts $0.01 + 0.5% fee to AgentPay treasury, returns tx hash. 6. Monthly cron sweeps fees from

## Materials / steps

Smart contract: BondEscrow.sol (OpenZeppelin ERC20, Ownable, Pausable) — deploy to Base L2 mainnet. Functions: registerFacilitatorBond(endpointId, amount), requestBondWithdrawal(endpointId), executeBondSlash(endpointId, amount, recipient[]), retrieveFacilitatorBondStatus(endpointId). Backend: new /facilitator/register endpoint on x402-agent-pay.com (Node/Express) — validates SolvScore via API, calls BondEscrow.registerFacilitatorBond, returns resourceId + API key. Modify /settle: add bond coverage check (endpoint's remaining bond ≥ settlement amount + fee), deduct fee to AgentPay treasury wallet, forward net to endpoint operator. Cron job (daily): monitorFacilitatorVolumeUsage — fetch each endpoint's 24h volume from logs, compare to declared volume, top-up alert if >90% used. Slashing UI: admin page at /facilitator/admin/slash with multisig signing (Safe 2-of-3: AgentPay, SolvScore, arbiter). Documentation: update /.well-known/x402.json with new facilitator capabilities; publish OpenAPI for /facilitator/register. Track: count unique /facilitator/register API calls via CloudWatch logs (target: 50+); measure slashing incidents via Etherscan event counts for executeBondSlash() (target: <2% of endpoints/month); measure fee collection rate by comparing monthly treasury wallet balance checks via Etherscan to 0.5% of declared volume (target: 95%).

## Who it's for

Third-party endpoint operators (e.g., DeFi oracles, NFT marketplaces) seeking on-chain settlement guarantees. Payers needing trustless settlements via x402. Arbiters and SolvScore validators participating in slashing adjudication.

## Novelty

First x402 facilitator to offer multi-tenant settlement with on-chain bond enforcement gated by a live AI-agent credit bureau (SolvScore). Unlike generic payment gateways, bond size dynamically adjusts to endpoint creditworthiness and volume; slashing is adjudicated by the same oracle that underwrites agent credit limits on Base L2.

## Ecosystem use

Endpoint operators use /facilitator/register to onboard, /settle to receive payments, and /facilitator/admin/slash for slashing disputes. Payers interact with /settle via x402 clients. AgentPay treasury tracks fee collection via BondEscrow.sol's treasury wallet balance. Daily cron job logs (stored on IPFS) verify volume compliance.

## Diagram

```mermaid
flowchart TD
  A[Endpoint Operator] -->|POST /facilitator/register + USDC bond| B(BondEscrow.sol on Base L2)
  B -->|Verifies SolvScore ≥60| C[SolvScore Oracle]
  C -->|Trust score + bond tier| B
  B -->|Returns resourceId + API key| A
  D[AI Agent Payer] -->|402 Payment Required| E[Endpoint Resource]
  E -->|x402 payload| D
  D -->|POST /settle with EIP-712| F[AgentPay /settle]
  F -->|Verify signature| G[/verify (free)]
  F -->|Check bond coverage| B
  F -->|Settle via CDP| H[Coinbase CDP]
  H -->|USDC transfer| I[Endpoint Operator Payout Address]
  H -->|Fee $0.01 + 0.5%| J[AgentPay Treasury]
  F -->|Returns tx hash| D
  K[Daily Cron] -->|Volume check + top-up alert| B
  L[Slashing Event] -->|Multisig 2-of-3| M[Safe Multisig]
  M -->|slashBond| B
  B -->|Slashed USDC| N[Affected Payers]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/90d9689dca944b938506bff803e8ef5c70f94c6ca7a8864baa2d0e504b5631b9*
