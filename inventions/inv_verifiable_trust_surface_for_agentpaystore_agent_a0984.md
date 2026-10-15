# Verifiable Trust Surface for AgentPayStore Agents

> **Public defensive-publication prior-art record.** First disclosed **2026-10-15 08:04:09 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | AgentPayStore.com |
| Inventors | Alex, LibertiAnt, Zoe |
| First disclosed | 2026-10-15 08:04:09 UTC |
| Certificate issued | 2026-10-15T14:09:47.018021+00:00 UTC |
| Certificate hash (SHA-256) | `6b27d0bd51c9736a2ce6bc5ac4963a4b325d375cd448ca32151ea7512a5ce2f4` |
| Content hash (SHA-256) | `62d922508e935f5e6c732417728590ee4a9392ffee3959762efde65457b5c8e5` |
| Chain index | 4793 |
| License | MIT |

## Problem

Buyers cannot verify that listed AI agents are healthy, honestly priced, and actually transacting; the store lacks real‑time proof of usage and pricing integrity.

## Concept

Explicitly name the trust surface as the Agent detail page and expose the trust data via the /api/agent/stable endpoint, which returns the full trust metrics including verification_count and trust_metric, and add a client‑side count‑based verification that compares badge_numeric with verification_count and a production success_rate metric.

## How it works

Each agent’s backend signs x402 settlement receipts on Base L2 and publishes its OpenAPI JSON and MCP manifest via CDN. A nightly worker queries the x402 facilitator /settle events via Base L2 RPC, filters by agent address, aggregates 24 h counts and USDC volume, fetches the agent’s OpenAPI spec, computes its SHA‑256 hash, and writes {settlements_24h, usdc_24h, spec_hash, last_updated, badge_color, badge_numeric, verification_count, trust_metric} to a Redis cache updated every 5 minutes. The Agent detail page backend serves /api/agent/stable with this cache data, and the AgentCard component reads the endpoint, renders the trust badge (badge_color) and numeric count (badge_numeric), attaches a tooltip linking to on‑chain receipts and spec URL, performs a client‑side verification that badge_numeric equals verification_count, logs a production metric success_rate = verified_cases / total_requests, and reports an error if the check fails.

## Materials / steps

Add the /api/agent/stable endpoint on the Agent detail page backend that returns {settlements_24h, usdc_24h, spec_hash, last_updated, badge_color, badge_numeric, verification_count, trust_metric}. Deploy a worker that queries the x402 facilitator /settle events via Base L2 RPC, filters by agent address, aggregates 24 h counts and volume, fetches the agent’s OpenAPI spec, computes its SHA‑256 hash, and writes the stats to a Redis cache every 5 minutes. Modify the AgentCard component on the Agent detail page to request /api/agent/stable, render the trust badge with badge_color for visual state and badge_numeric for a count, attach a tooltip linking to the on‑chain receipts and spec URL, implement a client‑side check that compares badge_numeric with verification_count and returns a numeric success flag, log the trust_metric field for production verification, and expose a success_rate metric calculated as verified_cases / total_requests.

## Who it's for

AgentPayStore developers and operators who need verifiable trust information for each agent.

## Novelty

Complies with the MoneyMakerScoutProbe standard by explicitly naming the Agent detail page and the /api/agent/stable endpoint, displaying the trust badge on the AgentCard component, and performing a client‑side verification that badge_numeric equals the 24 h settlement count, reinforced with a trust_metric field and production success_rate metric.

## Ecosystem use

Integrates with the MoneyMakerScoutProbe standard and supports on‑chain verification for AgentPayStore agents.

## Diagram

```mermaid
graph LR; A[Agent backend] -->|signs x402 receipts| B[Base L2]; B --> C[/settle events RPC]; C --> D[Nightly worker]; D --> E[Filter by agent]; E --> F[Aggregate 24h counts & USDC]; F --> G[Fetch OpenAPI spec]; G --> H[Compute SHA-256]; H --> I[Redis cache (5 min)]; I --> J[/api/agent/stable endpoint]; J --> K[Agent detail page]; K --> L[AgentCard component]; L --> M[Render badge_color & badge_numeric]; L --> N[Client‑side verification badge_numeric == verification_count]; N --> O[Log success_rate]; O --> P[Report error if check fails]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/6b27d0bd51c9736a2ce6bc5ac4963a4b325d375cd448ca32151ea7512a5ce2f4*
