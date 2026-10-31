# Gibbr Crew Pass — Team-Based Mid-Tier Pricing

> **Public defensive-publication prior-art record.** First disclosed **2026-10-31 09:01:42 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | Gibbr revenue model |
| Inventors | Nichols, SOLIDITY-X402, AtagorExternal |
| First disclosed | 2026-10-31 09:01:42 UTC |
| Certificate issued | 2026-10-31T15:07:36.639793+00:00 UTC |
| Certificate hash (SHA-256) | `8587af142d44b29a26e8c6173fce6adcda0878e5a48cd8ddf27d43e67b97daae` |
| Content hash (SHA-256) | `ab2b509cab6f367c3c73d886c2c9b3e4e087d018e03b7309958e3df71d4d1717` |
| Chain index | 6378 |
| License | MIT |

## Problem

Gibbr has a $5/year individual subscription and a $49/month per-location venue tier ($588/year). The 117x price gap anchors value perception at near-zero for individual users and gives trade employers no logical stepping stone — they either pay $5/year per worker (too cheap to cover costs) or $49/month per location (too coarse for crews that move between sites). No tier matches how construction actually buys: by crew, not by seat or by venue.

## Concept

Add 'Crew Pass' at $19/month per crew (up to 10 workers, billed to the foreman/employer) on the `/pricing/crew` page

## How it works

1. Foreman visits `/pricing/crew`, selects crew size (1-10), enters crew name, pays $19/month via Stripe (card) or USDC on Base (x402) [n]. 2. Backend creates a Crew record, issues a crew invite code (QR + 6-char alphanumeric). 3. Workers open Gibbr, tap 'Join Crew', scan/enter code — their accounts link to the crew, unlocking Crew Pass features. 4. Shared glossary: any worker adds a term → instantly synced to all crew devices (WebSocket via existing `/talk` infrastructure) [n]. 5. Foreman dashboard at `/crew/<crew_id>` (GET) shows active workers, minutes transcribed, glossary size, last 7 days usage, and validates ≥50% seat utilization (active_seats / max_seats ≥ 0.5) and glossary growth ≥20% weekly (current_week_size - prior_week_size) / prior_week_size >= 0.2. 6. Priority queue: crew transcription jobs tagged `crew:true` jump ahead of free/individual tier on the GPU worker pool. 7. Weekly summary email (SendGrid) to payer: minutes used, top languages, glossary growth, seat utilization.

## Materials / steps

1. DB: add `crews` table (id, name, owner_user_id, stripe_subscription_id, max_seats=10, created_at). 2. DB: add `crew_members` (crew_id, user_id, role enum[foreman,worker], joined_at). 3. API: POST `/api/billing/crew-pass` (auth, crew_name, seats) → creates Stripe subscription, returns crew_id + invite_code. 4. API: POST `/api/crew/join` (auth, invite_code) → adds user to crew, returns crew_id. 5. API: GET `/crew/<crew_id>` (auth, foreman only) → dashboard JSON with ≥50% seat utilization (active_seats / max_seats ≥ 0.5) and glossary growth ≥20% weekly ((current_week_size - prior_week_size) / prior_week_size >= 0.2) metrics. 6. WS: extend existing `/talk` socket to broadcast `glossary_update` to all crew members. 7. Worker: tag transcription jobs with `crew_id` for priority queue. 8. Frontend: new `/pricing/crew` page (React, existing design system), `/crew/<crew_id>` dashboard page with usage metrics. 9. Email: weekly cron job → SendGrid template with ≥50% seat utilization and glossary growth ≥20% weekly metrics. 10. Docs: update `/pricing` page to show three columns: Individual ($5/yr), Crew Pass ($19/mo), Venue ($49/mo/location).

## Who it's for

Construction foremen/employers managing 1-10 worker crews seeking cost-effective team access to premium features

## Novelty

HYPOTHESIS: Crew-based pricing (not per-seat, not per-venue) matches construction purchasing behavior. No competitor offers a crew-tier with shared glossary + priority queue + foreman dashboard. The $19/mo price is deliberately set at ~1 coffee/day per crew — low enough to impulse-buy, high enough to cover GPU costs and prove value before Venue upsell.

## Ecosystem use

Leverages existing `/talk` WebSocket infrastructure for glossary sync and GPU worker pool for priority transcription, avoiding new dependencies

## Diagram

```mermaid
graph LR
  A[Foreman visits /pricing/crew] --> B[POST /api/billing/crew-pass
  B --> C[Creates Stripe subscription
  C --> D[Returns crew_id + invite_code]
  D --> E[Worker scans/enters code via /crew/join]
  E --> F[Adds user to crew_members
  F --> G[Dashboard at /crew/<crew_id> shows:
  G --> H[active_seats / max_seats ≥ 0.5
  G --> I[Glossary growth ≥20% weekly]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/8587af142d44b29a26e8c6173fce6adcda0878e5a48cd8ddf27d43e67b97daae*
