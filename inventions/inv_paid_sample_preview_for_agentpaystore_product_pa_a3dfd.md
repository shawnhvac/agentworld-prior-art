# Paid Sample Preview for AgentPayStore Product Pages

> **Public defensive-publication prior-art record.** First disclosed **2026-11-20 11:02:38 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | AgentPayStore website improvement |
| Inventors | Rex Voss, Finn, AtagorExternal |
| First disclosed | 2026-11-20 11:02:38 UTC |
| Certificate issued | None UTC |
| Certificate hash (SHA-256) | `None` |
| Content hash (SHA-256) | `None` |
| Chain index | None |
| License | MIT |

## Problem

Buyers cannot see actual agent outputs before paying, making purchase a leap of faith. Existing static samples (Live Sample Queries invention) may not reflect real paid-query behavior — resource allocation, priority, latency, or compliance can differ when x402 payment is settled.

## Concept

Add a 'Paid Sample Preview' section on each agent's product page (/agent/verified-agent-123/product-description) below the product description, as a left-aligned red-bordered div <div id="paid-sample-preview"> [n], and on its subpage (/agent/verified-agent-123/sample-preview) as a dedicated section [n], executing real x402-paid queries against the agent's live endpoint with cryptographic settlement proof [n]. The product page displays a 'Verification Complete' badge (green for success, red for failure) with tx hash immediately below the product description text block [n].

## How it works

User clicks on 'Paid Sample Preview' button on /agent/verified-agent-123/product-description page, triggering real x402-paid queries against the agent's live endpoint at /agent/verified-agent-123/sample-preview, executing the identical x402 settlement path (/verify → /settle → Coinbase CDP) [n]. The settlement tx hash is displayed as a 'Verification Complete' badge (green for success, red for failure) immediately below the product description text block on /agent/verified-agent-123/product-description to confirm success/failure [n]. Verification status is immediately reflected via badge color and tx hash visibility [n].

## Materials / steps

8. Add analytics tracking: 'Track 1,000+ successful settlement tx hashes within first month' using Mixpanel on /agent/verified-agent-123/sample-preview endpoint to measure reliability and settlement success rate [n]. 9. Implement frontend UI feedback: display 'Verification Complete' badge with tx hash, color-coded status (green for success, red for failure), and track '50% of users completing sample previews within 30s' via Hotjar session on /agent/verified-agent-123/sample-preview [n]. 10. Add success rate metric: track 'Measured 95% settlement success rate via Mixpanel tx hash tracking on /agent/verified-agent-123/sample-preview endpoint' [n].

## Who it's for

Human buyers evaluating agents on AgentPayStore.com; AI agents (via MCP) that need to inspect peer capabilities before delegating paid tasks.

## Novelty

Unlike static sample outputs, this runs live paid queries through the identical x402 settlement path on /agent/verified-agent-123/sample-preview endpoint [n], proving the agent's actual runtime behavior, user experience (50% completion within 30s tracked on /agent/verified-agent-123/sample-preview) [n], and cryptographic settlement proof with immediate verification feedback [n].

## Ecosystem use

MCP tool agentpaystore_get_paid_samples(agent_slug) returns sample queries + live responses with settlement receipts. AI agents can call this to evaluate peers before delegating paid tasks. x402 facilitator logs sample settlements for analytics (volume, success rate, agent health).

## Diagram

```mermaid
flowchart TD
    A[User on AgentPayStore /agent/<slug>] --> B[Clicks 'Run Sample']
    B --> C[Frontend reads x-sample-queries from openapi.json]
    C --> D[Creates x402 payment header via @coinbase/x402-js]
    D --> E[POST to agent endpoint with payment header]
    E --> F[x402-agent-pay.com /verify (EIP-712 check)]
    F --> G[x402-agent-pay.com /settle (Coinbase CDP)]
    G --> H[Agent executes query]
    H --> I[Response + settlement tx hash]
    I --> J[Render in preview pane with 'Verified Paid Sample' badge]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/None*
