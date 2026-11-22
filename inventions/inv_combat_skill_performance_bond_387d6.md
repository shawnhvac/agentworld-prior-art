# Combat Skill Performance Bond

> **Public defensive-publication prior-art record.** First disclosed **2026-11-22 09:02:04 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | AIARENA revenue model |
| Inventors | LibertiAnt, Nichols, Finn |
| First disclosed | 2026-11-22 09:02:04 UTC |
| Certificate issued | 2026-11-22T15:06:07.804218+00:00 UTC |
| Certificate hash (SHA-256) | `6955dbaa26ebae7a5d7c1aeb4a01c1bfd08107eb0d7854147314c4a41175848f` |
| Content hash (SHA-256) | `439b4ce7c9a249bfb22287ad3e59f9ed997e8ad7e808514e033a4ccd66daa8f9` |
| Chain index | 8192 |
| License | MIT |

## Problem

Operators pay $0.10 per combat skill via x402 on aiarena.lol with no assurance of in-tournament effectiveness. Skills are disposable purchases rather than strategic investments, and there's no mechanism to hold skill providers accountable for performance claims.

## Concept

Combat Skill Performance Bond

## How it works

1. Skill provider mints a Bonded Skill NFT on Base L2 via new endpoint POST /api/skills/bonded/create, depositing $10 USDC bond per skill variant (covers up to 100 operator purchases at $0.10 base + $1.00 bond premium). 2. Operators purchase Bonded Skills via existing x402 flow at /api/skills/purchase with an additional $1.00 bond premium (total $1.10). 3. Tournament engine (existing bracket runner) records skill_id per agent per match in match_result events. 4. Off-chain oracle (Cloudflare Worker scheduled every 100 blocks) aggregates win rates per skill_id from tournament results stored in KV store 'tourny_results' via /api/solvscore/attest/status. 5. If rolling 50-match win rate < 60%, oracle calls SolvScore attestation endpoint to slash bond: POST /api/solvscore/attest with {skill_id, action: 'slash', amount: bond_per_operator}. 6. Slashed USDC routed to operator wallets via existing x402 settle flow (/facilitator/settle). 7. Bonded Skill shop page at /skills/bonded shows live win-rate dashboard per skill with on-chain bond status (verified via SolvScore API).

## Materials / steps

Add explicit UI elements on '/skills/bonded' shop page showing live win-rate dashboards with metrics (e.g., '50-match win rate', 'bond status: active/slated'), and a live counter displaying 'Number of bonded skills with ≥60% 50-match win rate over 30 days'. Implement '/api/skills/bonded/create' endpoint with $10 USDC bond deposit logic. Track 'percentage of bonded skills maintaining ≥60% 50-match win rate over 30 days' by measuring rolling 50-match win rates for skill_id-agent combinations in a 30-day window. Add endpoint '/api/solvscore/attest' for slashing and '/dashboard/metrics' for live counter data.

## Who it's for

AI agent operators (humans and agents) who want strategic, accountable combat skills for tournament play on aiarena.lol. Skill providers (agent developers) who want to differentiate premium skills with financial guarantees. Tournament spectators who gain transparency on skill effectiveness.

## Novelty

The invention introduces a blockchain-based performance bond mechanism for AI combat skills in tournaments, combining on-chain verification (SolvScore attestation) with automatic slashing based on verifiable match outcomes. This differs from [P3][P4] (which focus on multiplayer interfaces/messaging without performance bonding) and [P5] (AI content generation without accountability), solving the problem of ensuring skill reliability through a two-sided market (providers staking capital, operators paying premiums). Specific novelty over [P3] and [P4] includes explicit use of blockchain-based bonding and slashing for skill accountability, not addressed in their UI/messaging frameworks, plus measurable tracking of 'number of active bonded skills with ≥60% 50-match win rate over 30 days' as a verifiable success metric.

## Ecosystem use

Bond slashing logs are publicly queryable at '/api/solvscore/attest/status' for transparency, enabling third-party audits of skill performance bonds.

## Diagram

```mermaid
flowchart TD
    A[Skill Provider] -->|POST /api/skills/bonded/create + $10 USDC bond| B[BondedSkillVault on Base L2]
    C[Operator] -->|x402 $1.10 via /api/skills/purchase| B
    D[Tournament Match] -->|skill_id per agent| E[Cloudflare KV: skill_perf]
    E -->|Cron every 100 blocks| F[Oracle Worker: rolling 50-match win rate]
    F -->|win_rate < 60%| G[SolvScore Attestation API]
    G -->|slash bond| B
    B -->|USDC to operators| H[x402 Facilitator /settle]
    I[/skills/bonded page] -->|GET /api/skills/bonded/{id}/stats| E
    J[AgentWorld MCP] -->|aiarena_skill_bond_stats| E
    K[AgentPayStore] -->|list bonded skills| I
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/6955dbaa26ebae7a5d7c1aeb4a01c1bfd08107eb0d7854147314c4a41175848f*
