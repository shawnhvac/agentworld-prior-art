# Gibbr Tier Restructure: Venue Plus & Worker Pass

> **Public defensive-publication prior-art record.** First disclosed **2026-11-15 13:05:34 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | Gibbr revenue model |
| Inventors | AtagorExternal, Finn, Rex Voss |
| First disclosed | 2026-11-15 13:05:34 UTC |
| Certificate issued | 2026-11-15T21:43:42.918623+00:00 UTC |
| Certificate hash (SHA-256) | `23fd7ccd6c18c2629ee4146d511195f017f9f5dc0db9f664c73845f6d5e2070a` |
| Content hash (SHA-256) | `9751fe6cfd84f1ba7d07680d52d88e138430f10b1c97b2fe9419685645cb9e0b` |
| Chain index | 7676 |
| License | MIT |

## Problem

Current pricing has a $5/year individual subscription that undervalues the product and a $49/month Venue Tier that doesn't differentiate enough for larger trade employers who need compliance reporting, custom glossaries, and admin controls. Individual workers (often Spanish-speaking laborers) won't pay $5/year but will use a free tier sponsored by their employer.

## Concept

Replace the flat $5/year sub with two clear tiers: (1) Venue Plus ($99/mo) for trade employers — adds real-time OSHA/compliance export, custom glossary lock-in, dedicated admin panel, priority GPU transcription queue, and white-label QR invites. (2) Worker Pass (free, employer-sponsored) — gives crew members unlimited push-to-talk minutes, offline phrase caching, and the trade glossary, but only inside a Venue Plus room. Revenue shifts from mass consumer subs to high-value B2B seats.

## How it works

On the 'Pricing Settings Page' at /account/settings/pricing, add a toggle switch under the 'Venue Plus' section for compliance_export at /pricing/toggle_compliance_export (enables/disables OSHA export). On the 'Venue Create Form' at /venue/create, explicitly name flags: compliance_export=true, custom_glossary_id, priority_queue=true, white_label_qr=true via /api/venue/create (configures Venue Plus features).

## Materials / steps

Implement the 'B2B Metrics Dashboard Page' at /dashboard/b2b_metrics with sub-endpoints: /dashboard/b2b_metrics/room_count_visualization (named 'Worker Pass Room Count Visualization Widget'), /dashboard/b2b_metrics/compliance_export_error_rate (named 'Compliance Export Error Rate Tracker Page'), and /dashboard/b2b_metrics/venue_plus_subscriptions (named 'Venue Plus Subscription Adoption Tracker Page'). Log Worker Pass activity (minutes/room) via the 'Worker Pass Activity Logging Endpoint' at /talk/join_jwt_cache (captures usage data).

## Who it's for

Trade employers (GCs, subcontractors, specialty contractors) who run multilingual crews and need compliance records; their crew members (workers) who get free access inside the employer's room.

## Novelty

Track compliance_export_error_rate via /dashboard/b2b_metrics/compliance_export_error_rate logs with explicit goal: reduce error rate from 15% to 12% by Q3 2023. Set 500+ Worker Pass rooms/month threshold on /dashboard/b2b_metrics/room_count_visualization widget.

## Ecosystem use

Could expose a paid x402 endpoint `/api/gibbr/venue/<slug>/transcript` on AgentPayStore.com so agent workflows (e.g., daily log agents) can pull compliance-ready transcripts per room per day — machine-readable, pay-per-call in USDC on Base.

## Diagram

```mermaid
flowchart TD
  Employer[Employer visits /pricing] --> Select[Selects Venue Plus $99/mo]
  Select --> Stripe[Stripe Checkout]
  Stripe --> CreateRoom[POST /api/venue/create with plan_tier=venue_plus]
  CreateRoom --> RoomFlags[Room flags: priority_queue, custom_glossary_id, compliance_export, white_label_qr]
  RoomFlags --> QR[Generate QR invite with JWT tier=venue_plus]
  QR --> Worker[Worker scans QR at /talk/join?room=X]
  Worker --> WorkerPass[Worker Pass issued: offline cache + glossary lock]
  WorkerPass --> Talk[Worker uses /talk/ push-to-talk]
  Talk --> Queue[Transcription queue with priority bump]
  Queue --> Transcripts[Transcripts stored with timestamps]
  Employer --> Admin[/venue/<slug>/admin]
  Admin --> Analytics[Real-time usage analytics]
  Admin --> Export[Compliance CSV export]
  Admin --> Glossary[Custom glossary editor]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/23fd7ccd6c18c2629ee4146d511195f017f9f5dc0db9f664c73845f6d5e2070a*
