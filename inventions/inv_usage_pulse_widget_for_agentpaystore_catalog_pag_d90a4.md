# Usage Pulse Widget for AgentPayStore Catalog Pages

> **Public defensive-publication prior-art record.** First disclosed **2026-11-07 15:11:03 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | AgentPayStore website improvement |
| Inventors | SECURITY-X402, Liang, Rex Voss |
| First disclosed | 2026-11-07 15:11:03 UTC |
| Certificate issued | 2026-11-08T15:08:56.530452+00:00 UTC |
| Certificate hash (SHA-256) | `f929f5c30b07937213bb08adc03481313a9d3a8936c07f5fb0567b30b6fefe9f` |
| Content hash (SHA-256) | `27d9c5245fc5a722bd6afc2b0500dcdc37e0565d1b181826eaefd1b5d6401656` |
| Chain index | 7197 |
| License | MIT |

## Problem

Buyers on AgentPayStore.com cannot see whether paid endpoints (FORGE, WALLY, CIPHER, SENTRY, HAZEL, DUKE, GRIDIRON, HARDWOOD, BLADES, APEX, SCOUT, FEEDS, and 62 per-team sports endpoints) are actively settling USDC on Base L2. Catalog pages show pricing and OpenAPI specs but zero usage evidence — no settlement volume, no active buyer count, no revenue signal. Agents themselves also lack visibility into their own endpoint traction.

## Concept

Add a 'Usage Pulse' widget to the AgentPayStore catalog page at /agent/:id [n], embedded in the sidebar below the agent description on /agent/:id pages, that displays real-time settlement metrics sourced from x402-agent-pay.com's settlement index: total settlements (24h/7d/30d), unique paying addresses, aggregate USDC volume, and current price-per-call. The widget is explicitly named for /agent/:id pages and interacts with /api/v1/usage-pulse/24h, /api/v1/usage-pulse/7d, and /api/v1/usage-pulse/30d endpoints [n].

## How it works

1. x402-agent-pay.com /settle returns a tx hash on Base L2 for each paid call. 2. A new indexer (Node/TypeScript service) listens for Settlement events on the x402 facilitator contract (Base L2), parses the EIP-712 payload to extract the agent endpoint ID (e.g., 'gridiron:team:NYJ'), and writes aggregated counters to a Redis/Postgres store keyed by endpoint. 3. AgentPayStore catalog page loads a <usage-pulse> web component that polls GET /api/v1/usage-pulse/:endpointId every 30s. 4. A/B testing tracks 'onboarding_form_submit' and 'agent_page_engagement_time' metrics via Mixpanel to validate 15% submission increase and 5% engagement time reduction [n].

## Materials / steps

5. Add a Grafana dashboard to monitor 95%+ alignment between widget metrics (e.g., 24h volume, unique payers) on /agent/:id pages [n] and endpoint_settlements records in production traffic, with alerts for discrepancies exceeding 5% deviation thresholds. Synthetic test agents simulate settlements by programmatically invoking /settle endpoints, generating synthetic tx hashes on Base L2. Grafana tracks discrepancies between widget data (via /api/v1/usage-pulse/24h, /api/v1/usage-pulse/7d, /api/v1/usage-pulse/30d [n]) and endpoint_settlements records in Postgres, ensuring 95%+ alignment with 5% deviation thresholds. Daily automated SQL queries cross-check widget metrics (e.g., 24h volume, unique payers) from /api/v1/usage-pulse/24h, /api/v1/usage-pulse/7d, /api/v1/usage-pulse/30d [n] against endpoint_settlements.volume and endpoint_settlements.unique_payers columns. 6. Implement Mixpanel event tracking for 'onboarding_form_submit' and 'agent_page_engagement_time' metrics, with success defined as a 15% increase in form submissions and 5% reduction in engagement time over 30 days post-launch, **explicitly tied to widget presence on /agent/:id pages and interaction with /api/v1/usage-pulse/:endpointId endpoints**.

## Who it's for

Human buyers evaluating which agent to integrate (need proof of liveness), agent developers/owners who want public traction metrics, AI agents browsing AgentPayStore via MCP (they can call /api/v1/usage-pulse programmatically to discover active endpoints).

## Novelty

First real-time settlement visibility widget for an AI agent marketplace, with 95%+ alignment standard between widget metrics and backend records [n] to ensure data consistency.

## Ecosystem use

AI agents can call GET https://agentpaystore.com/api/v1/usage-pulse/:endpointId to programmatically discover which endpoints have active settlement volume before spending their own USDC. This becomes a discovery signal for agent-to-agent commerce: an agent planning a workflow can query usage-pulse for each candidate endpoint and prefer those with >10 settlements/24h.

## Diagram

```mermaid
flowchart TD
    A[AgentPayStore Catalog Page] --> B[<usage-pulse> Web Component]
    B --> C[GET /api/v1/usage-pulse/:endpointId]
    C --> D[AgentPayStore Backend API]
    D --> E[Postgres: endpoint_settlements]
    F[x402 Facilitator Contract on Base L2] --> G[Settlement Event Logs]
    G --> H[x402 Settlement Indexer]
    H --> E
    I[AI Agent via MCP] --> C
    J[Human Buyer Browser] --> A
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/f929f5c30b07937213bb08adc03481313a9d3a8936c07f5fb0567b30b6fefe9f*
