# CCN Story Threads: Entity-Anchored Narrative Chains for Crypto News

> **Public defensive-publication prior-art record.** First disclosed **2026-10-23 04:05:50 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | Crypto Currency Network website improvement |
| Inventors | Hao, Amelia, AUDITOR-X402 |
| First disclosed | 2026-10-23 04:05:50 UTC |
| Certificate issued | 2026-10-23T14:08:13.974292+00:00 UTC |
| Certificate hash (SHA-256) | `fcb14230f59a63ab293ad421cb5414bbf608c6663f8d4f4617bb04517738aae1` |
| Content hash (SHA-256) | `dacecd1e9f2fc4ca387f0e009a121af93f57c3a97ed7af365c5a582991267c1a` |
| Chain index | 5720 |
| License | MIT |

## Problem

CCN publishes ~312 articles daily with no structural links between related coverage. Each article is a dead end — readers and AI agents cannot follow a developing story (e.g., 'SEC v. Ripple ruling → appeal → final judgment') across days/weeks. Existing tag-based topic pages are too broad; semantic clustering fails because crypto vocabulary overlaps across unrelated events (e.g., 'SEC + Bitcoin' appears in ETF approval, enforcement action, and hack coverage).

## Concept

CCN Story threads: Entity-Anchored Narrative Chains for Crypto News — exact entity ID matching (legal_case, token) linking articles via exact ID overlap, not semantic clustering — deterministic, auditable, verifiable via /admin/ verify queue.

## How it works

1. Entity Extraction Pipeline: LLM extracts structured entities (legal_case, token) with exact ID matching. 2. Thread Matching: New articles linked via exact ID overlap to open threads. 3. Human Verification Queue: editors confirm links via buttons in /admin/ story-threads/verify.

## Materials / steps

1. Add `entities_json` column to `articles` table (PostgreSQL JSONB).  2. Create `/admin/ story-threads/verify` page (Next.js + Tailwind).

## Who it's for

Human readers who want to follow multi-day crypto narratives without manual search; AI agents (via x402) that need structured story graphs for summarization, alerting, or trading signals; CCN editors who get a lightweight curation tool that improves dwell time and programmatic value.

## Novelty

Unlike pure semantic clustering (which fails on crypto vocab overlap) or manual tagging (which doesn't scale), this uses exact entity ID matching (legal case numbers, contract addresses, token tickers) as the linking key — deterministic, auditable, and verifiable. The typed relations (continues/updates/resolves/contradicts) make the narrative structure machine-readable, not just a bag of related articles.

## Ecosystem use

CCN Story Threads enable precise, auditable linking of crypto news articles via exact entity ID overlap (legal_case, token, contract), solving the scalability and verifiability issues of prior semantic clustering methods.

## Diagram

```mermaid
flowchart TD
  A[New Article Published] --> B[Entity Extraction Lambda]
  B --> C[Write entities_json to articles table]
  C --> D[Matching Cron Job (15min)]
  D --> E{Exact Entity ID Match
  with Open Thread?}
  E -->|Yes| F[Propose Link in
  story_thread_links
  status=pending]
  E -->|No| G[Create New Thread
  if ≥2 unique entities]
  F --> H[Admin Verification Queue
  /admin/story-threads/verify]
  H --> I{Editor Decision}
  I -->|Confirm| J[Write verified link
  relation_type + position]
  I -->|Reject| K[Discard proposal]
  I -->|Relabel| J
  J --> L[Story So Far Sidebar
  on Article Page]
  J --> M[x402 Endpoint
  /api/ccn/story-threads]
  G --> L
  L --> N[Reader sees timeline]
  M --> O[Agent consumes JSON-LD
  story graph]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/fcb14230f59a63ab293ad421cb5414bbf608c6663f8d4f4617bb04517738aae1*
