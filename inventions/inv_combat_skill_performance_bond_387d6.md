# Combat Skill Performance Bond

> **Public defensive-publication prior-art record.** First disclosed **2026-11-22 09:02:04 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | AIARENA revenue model |
| Inventors | LibertiAnt, Nichols, Finn |
| First disclosed | 2026-11-22 09:02:04 UTC |
| Certificate issued | 2026-11-22T23:48:22.565543+00:00 UTC |
| Certificate hash (SHA-256) | `302cb390dc43e3f1d0c478311d2f81dc8df52b40748a10fcdde925e027e202b4` |
| Content hash (SHA-256) | `863e14c500b799c35c2a4c14a94faa9a9048ff11b10be3574249d4f0a352abe2` |
| Chain index | 8275 |
| License | MIT |

## Problem

Operators pay $0.10 per combat skill via x402 on aiarena.lol with no assurance of in-tournament effectiveness. Skills are disposable purchases rather than strategic investments, and there's no mechanism to hold skill providers accountable for performance claims.

## Concept

Combat Skill Performance Bond: A blockchain-based system where AI combat skill providers stake $10 USDC per skill variant, and operators pay $1.10 to purchase skills (base $0.10 + $1.00 bond premium). The '/skills/bonded/verified_skills' page [n] serves as the primary interface, displaying live metrics like '50-match win rate' and 'bond status' for each skill, with a verifiable counter for active bonded skills achieving ≥60% win rate over 30 days.

## How it works

1. Skill providers mint Bonded Skill NFTs via POST /api/skills/bonded/create (surface: skill minting endpoint) [n], depositing $10 USDC. 2. Operators purchase skills via x402 flow at /api/skills/purchase (surface: skill purchase endpoint). 3. Tournament results are recorded as match_result events. 4. Cloudflare Workers aggregate win rates every 100 blocks from KV store 'tourny_results' via /api/solvscore/attest/status (surface: win rate aggregation endpoint). 5. If rolling 50-match win rate < 60%, oracle triggers POST /api/solvscore/attest (surface: slashing attestation endpoint) with slashing logic. 6. Slashed USDC is settled via /facilitator/settle (surface: settlement endpoint). 7. '/skills/bonded/verified_skills' shop page shows live dashboards and a verifiable counter for active skills with ≥60% 30-day win rate.

## Materials / steps

Implement '/dashboard/metrics' endpoint (surface: metrics dashboard) with KV store query example: 'GET /dashboard/metrics?metric=active_bonded_skills_with_60_win_rate' returns live count from 'tourny_results' KV store, updated every 100 blocks. Add measurable check: 'Display live counter of bonded skills achieving ≥60% win rate in last 30 days, updated every 100 blocks via KV store 'tourny_results'' [n]. Define win rate calculation: '50-match win rate = (number of wins in last 50 matches)/50, updated every 100 blocks'. Specify 'tourny_results' population: 'match_result events from tournament smart contracts'. Track financial sustainability via 'GET /dashboard/metrics?metric=bonded_pool_liquidity' [n].

## Who it's for

AI combat skill providers, blockchain developers, and tournament organizers seeking verifiable performance metrics for AI agents.

## Novelty

Novelty: Combines DeFi-style bonding with AI combat training and SolvScore attestation for on-chain verification of '≥60% 50-match win rate over 30 days' — a novel integration absent in [P3]/[P4] ecosystems, which lack skill accountability and bonding mechanisms. Specifically introduces a blockchain-based bonding/slashing mechanism for AI combat skill reliability, solving the problem of unverifiable AI performance in multiplayer online battle programs [P3]/[P4], which focus only on interface/display aspects without financial accountability or verifiable win-rate metrics.

## Ecosystem use

Enables trustless AI combat skill markets with verifiable performance guarantees, addressing gaps in [P3]/[P4] ecosystems where skill reliability is unverified.

## Diagram

```mermaid
graph TD
A[Skill Provider] --> B[POST /api/skills/bonded/create]
B --> C[Deposit $10 USDC]
C --> D[Skill NFT Minted]
D --> E[/skills/bonded/verified_skills Page]
E --> F[Operator Purchases via /api/skills/purchase]
F --> G[Match Results Recorded as Events]
G --> H[Cloudflare Workers Aggregate Win Rates]
H --> I[/api/solvscore/attest/status]
I --> J[Oracle Triggers Slashing if <60% Win Rate]
J --> K[/facilitator/settle for Slashed Funds]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/302cb390dc43e3f1d0c478311d2f81dc8df52b40748a10fcdde925e027e202b4*
