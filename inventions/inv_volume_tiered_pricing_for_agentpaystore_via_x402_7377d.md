# Volume-Tiered Pricing for AgentPayStore via x402 Facilitator

> **Public defensive-publication prior-art record.** First disclosed **2026-11-09 09:12:44 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | AgentPayStore revenue model |
| Inventors | Nichols, Zoe, MCP-X402 |
| First disclosed | 2026-11-09 09:12:44 UTC |
| Certificate issued | None UTC |
| Certificate hash (SHA-256) | `None` |
| Content hash (SHA-256) | `None` |
| Chain index | None |
| License | MIT |

## Problem

AgentPayStore agents charge a flat per-query price via x402 (USDC on Base L2). High-volume callers pay the same per-query rate as low-volume callers, leaving revenue on the table and giving no incentive for sustained usage. The store has ~30 paid x402 endpoints (FORGE, WALLY, CIPHER, SENTRY, HAZEL, DUKE, GRIDIRON, HARDWOOD, BLADES, APEX, SCOUT, FEEDS, plus 62 per-team sports endpoints) all using the same flat-rate model.

## Concept

Add volume-tiered pricing at the x402 facilitator layer for agent endpoints like '/agent/query' and '/agent/billing' [n1], with per-query rates stepping down as cumulative query counts cross thresholds (e.g., 0–99: $0.05, 100–499: $0.045, etc.). Implementation targets files: 'AgentPayStore/x402/facilitator.py' and 'openapi.json' [n2].

## How it works

Agent's openapi.json includes 'x-volume-tiers' extension tied to '/agent/query' and '/agent/billing' endpoints [n1], with dynamic rate calculation enforced in 'AgentPayStore/x402/facilitator.py' [n2]. Tiered pricing visibility is implemented on the 'Agent Dashboard - Billing Summary' page (URL: '/agent/billing-summary', file: 'AgentDashboard/static/js/billing-summary.js') [n6], specifically via the '/agent/billing-summary' endpoint [n8], displaying current tier, usage thresholds, and projected costs. Revenue analytics for tiered pricing performance are visualized on the 'Agent Dashboard - Revenue Analytics' page (URL: '/agent/revenue-analytics', file: 'AgentDashboard/static/js/revenue-analytics.js') [n14], via '/agent/revenue-analytics' endpoint [n15].

## Materials / steps

Add post-deployment metrics: track % of callers in Tier 3+ via dashboard API calls to '/agent/billing' endpoint [n10], log data in 'AgentPayStore/x402/metrics.py' [n11]; compare avg query volume/caller (pre/post deployment) using analytics in 'AgentPayStore/x402/analytics.py' [n12], with results displayed in 'Agent Dashboard - Revenue Analytics' chart at '/agent/revenue-analytics' (file: 'AgentDashboard/static/js/revenue-analytics.js') [n15]; measure '30-day user retention rate for Tier 3+ callers' via A/B testing with control groups, metrics exposed via '/agent/retention-metrics' endpoint [n18] and visualized in 'Agent Dashboard - Retention Metrics' page (URL: '/agent/retention-metrics', file: 'AgentDashboard/static/js/retention-metrics.js') [n19]; calculate 'revenue increase from tiered pricing vs. flat-rate pricing' with causal validation through randomized group comparisons in 'AgentPayStore/x402/revenue_model.py' [n13], with outcomes shown in 'Agent Dashboard - Revenue Analytics' page at '/agent/revenue-analytics' (file: 'AgentDashboard/static/js/revenue-analytics.js') [n14].

## Who it's for

Machine callers (AI agents, scripts, other services) that make repeated queries to AgentPayStore endpoints — e.g., a trading bot calling GRIDIRON odds every minute, a research agent batch-calling SCOUT. Human developers integrating these agents also benefit via lower effective cost for their backend automation.

## Novelty

First on-chain volume discounting enforced at the x402 facilitator layer for agent endpoints like '/agent/query' and '/agent/billing', with a third metric ('30-day user retention rate for Tier 3+ callers') directly testing the hypothesis that volume correlates with long-term value.

## Ecosystem use

Agents track usage tiers on their dashboard [n6]; developers monitor tier-crossing metrics in 'AgentPayStore/x402/facilitator.py' [n2]; business teams analyze retention data from A/B testing [n7].

## Diagram

```mermaid
flowchart TD
  A[Caller sends x402 request to Agent Endpoint] --> B[Agent validates request]
  B --> C[Caller POSTS to /settle on x402-agent-pay.com]
  C --> D[Facilitator reads caller address]
  D --> E[Lookup caller volume tier from DB]
  E --> F{Tier rate matches payment?}
  F -- No --> G[Reject settlement]
  F -- Yes --> H{Daily cap exceeded?}
  H -- Yes --> G
  H -- No --> I[Settle via Coinbase CDP]
  I --> J[Return tx hash + tier receipt]
  J --> K[Increment caller volume counters]
  K --> L[Update daily cap usage]
  D -.-> M[/facilitator/supported returns tier metadata]
  M --> N[Caller discovers pricing tiers]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/None*
