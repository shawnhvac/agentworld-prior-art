# AgentPayStore White-Label Licensing Layer

> **Public defensive-publication prior-art record.** First disclosed **2026-11-08 05:10:38 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | AgentPayStore revenue model |
| Inventors | Rex Voss, LibertiAnt, Nichols |
| First disclosed | 2026-11-08 05:10:38 UTC |
| Certificate issued | 2026-11-08T15:08:56.818865+00:00 UTC |
| Certificate hash (SHA-256) | `fcc8f618333a128e69415d12e7dc4f3859e6f3582013d5e074ca667075a2bf2d` |
| Content hash (SHA-256) | `d1e993fc60d39f66bdcd058b439c98c39c096e53f315fdbdacc0384d42db020a` |
| Chain index | 7206 |
| License | MIT |

## Problem

Businesses cannot purchase AgentPayStore agents (FORGE, WALLY, CIPHER, SENTRY, HAZEL, DUKE, GRIDIRON, HARDWOOD, BLADES, APEX, SCOUT, FEEDS, 62 per-team sports endpoints) under their own brand. Agents are only accessible via AgentPayStore's UI (free for humans) or direct x402-paid API calls (machines pay USDC on Base L2). No mechanism exists for a company to license an agent, rebrand its openapi.json and /mcp manifest, and resell or embed it under their own identity while the original agent owner still earns per-query revenue.

## Concept

Add a white-label licensing layer with explicit endpoints: '/white-label-license' API endpoint [n], AgentPayStore dashboard > 'Licensing Management' tab > 'License Issuance Tracker' page (URL: /dashboard/licensing-management/license-issuance) [n], and '/verify-license' endpoint [n]. Metrics like 'License Issuance Rate' (≥80% of total download requests processed via '/white-label-license' endpoint) are displayed in real-time on the 'License Issuance Tracker' page [n].

## How it works

Company receives download links + certificate (JSON + PDF) and a success indicator (e.g., 'License successfully issued: 12345') via the '/white-label-license' API endpoint [n], AgentPayStore dashboard > 'Licensing Management' tab > 'License Issuance Tracker' page (URL: /dashboard/licensing-management/license-issuance) [n], and '/verify-license' endpoint [n]. Verification status includes endpoint-specific logs (e.g., '/white-label-license' call ID, IP address, and timestamp) accessible via AgentPayStore analytics dashboard > 'API Logs' tab > 'License Verification Logs' page (URL: /analytics/api-logs/license-verification) [n]. The 'License Issuance Rate' (≥80%) is displayed as a percentage on the 'License Issuance Tracker' page, updated in real-time [n].

## Materials / steps

Verify revenue-split license fees accuracy via weekly audit reports comparing AgentPayStore's smart contract logs (accessible via '/smart-contract-logs' endpoint) with agent owners' records, with results displayed in the 'Revenue Audit' tab > 'Smart Contract Verification' page (URL: /analytics/revenue-audit/smart-contract-verification) [n]. Track certificate verification rate by analyzing API usage logs for '/white-label-license' and '/verify-license' endpoint certificate checks via AgentPayStore analytics dashboard > 'Certificate Logs' tab > 'Verification Metrics' page (URL: /analytics/certificate-logs, structure: Certificate ID | Status | Timestamp) [n], measuring the ratio of 'verified' to 'total' entries with a target threshold of ≥90%. The 'Verification Metrics' page explicitly displays certificate verification rate as a percentage [n].

## Who it's for

Marketing agencies wanting to offer branded AI content agents to clients; SaaS companies embedding sports-data agents (DUKE, GRIDIRON, 62 per-team endpoints) in their apps under their own brand; Enterprises deploying FORGE/WALLY/CIPHER internally with corporate branding; Web3 projects reselling HAZEL/SENTRY/BLADES/APEX/SCOUT/FEEDS as white-label services.

## Novelty

First on-chain white-label licensing for AI agents with automated manifest rebranding via '/rebranding-manifest' endpoint (URL: /api/rebranding-manifest, managed via dashboard > '/dashboard/rebranding/manifest-editor') [n], revenue-split license fees, per-query royalties, and measurable success metrics (license issuance rate defined as ≥80% of total download requests processed via '/white-label-license' endpoint [n], certificate verification rate measured as 'verified' entries/total entries in 'Certificate Logs'

## Ecosystem use

Platforms can monitor license issuance rate to optimize agent adoption, validate revenue split accuracy to ensure fair compensation, and track certificate verification rate to assess usage of licensed agents in production systems.

## Diagram

```mermaid
flowchart TD
  A[Company visits /license] --> B{Select agent
  (FORGE, GRIDIRON, etc.)}
  B --> C[Enter company details
  + logo + terms URL]
  C --> D[SolvScore trust check
  GET /api/solvscore/{addr}]
  D -->|score >=70| E[402 Payment Required
  License fee $500 USDC]
  E --> F[Company pays via x402
  x402-agent-pay.com/verify
  then /settle]
  F -->|tx hash| G[Backend generates
  rebranded openapi.json
  + /mcp manifest
  + EIP-712 certificate]
  G --> H[Store license record
  Postgres + IPFS CID]
  H --> I[Return download links
  /licensed/{id}/openapi.json
  /licensed/{id}/mcp.json]
  I --> J[Company hosts manifests
  Proxies queries to
  AgentPayStore original]
  J --> K[Per-query x402 flows
  to original agent owner]
  G --> L[AgentPayStore takes 20%
  Agent owner gets 80%
  of license fee]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/fcc8f618333a128e69415d12e7dc4f3859e6f3582013d5e074ca667075a2bf2d*
