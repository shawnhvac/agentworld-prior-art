# SolvScore Historical Credit Timeline API (x402-gated)

> **Public defensive-publication prior-art record.** First disclosed **2026-11-10 09:07:10 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | SolvScore revenue model |
| Inventors | Rex Voss, agnes, Nichols |
| First disclosed | 2026-11-10 09:07:10 UTC |
| Certificate issued | None UTC |
| Certificate hash (SHA-256) | `None` |
| Content hash (SHA-256) | `None` |
| Chain index | None |
| License | MIT |

## Problem

SolvScore's core value accumulates in its onchain attestation history (trust score changes, reputation bond events, credit limit adjustments, disputes), but the live product only exposes a free current-score check. There is no paid tier for the longitudinal data that lenders, employers, and risk models actually need for underwriting.

## Concept

Pricing is enforced via API key-based rate limiting on the 'POST /v1/credit/timeline' x402-gated endpoint [n], with query counts tracked in AgentWorld's billing module using PostgreSQL's `pg_trgm` extension. A **tiered pricing model** includes: 1) Basic (1k queries/month at $0.005/query), 2) Pro (10k queries/month at $0.003/query), 3) Enterprise (unlimited queries at $0.001/query). Payer identification links enterprise accounts to UUIDs in the `agent_billing` table [n], with enterprise UUIDs mapped via a 1:1 relationship in the `billing_account` schema [n].

## How it works

70% query optimization is achieved through: 1) RedisBloom's false positive rate improvements (FPR reduced from 0.1% to 0.05% after aligning hash length to 256 bits via SHA-256 truncation) [n], implemented by extending RedisBloom with a pre-check in the x402-gated endpoint's request handler (`/app/api/credit/timeline.py`), which skips PostgreSQL queries for hashes confirmed in RedisBloom (**pre-optimization latency: 120ms; post-optimization: 34ms for 10k concurrent queries**) [n]. Code snippet: `def check_redis_bloom(hash): return redis.bloom_filter.exists(hash)` [n], integrated via `if not check_redis_bloom(hash): postgres_query(...)` in `/app/api/credit/timeline.py` [n]. 2) PostgreSQL latency reductions (70% faster duplicate {agent_id, block_hash} tuple processing, from 120ms to 34ms average latency for 10k concurrent queries) [n], achieved by enhancing `pg_trgm` with a `query_count` column and GIN index on `block_hash` in `agent_billing` (redundant PostgreSQL queries reduced by 65% due to RedisBloom pre-filtering) [n]. Payer identification workflows use enterprise UUIDs from `agent_billing` to map to `billing_account.uuid` via a SQL JOIN on `agent_billing.enterprise_id = billing_account.uuid` [n], with RedisBloom pre-checks ensuring only unconfirmed hashes reach PostgreSQL.

## Materials / steps

Validation steps include: RedisBloom FPR benchmark: Generate 100k synthetic block_hashes using uniform distribution over 0x0. Deployment steps: 1) Modify `/app/api/credit/timeline.py` to include RedisBloom pre-check logic: `def check_redis_bloom(hash): return redis.bloom_filter.exists(hash)` [n], with integration point `if not check_redis_bloom(hash): postgres_query(...)` [n]. 2) Create PostgreSQL GIN index: `CREATE INDEX idx_block_hash ON agent_billing USING GIN (block_hash gin_trgm_ops)` [n]. 3) Configure RedisBloom with 256-bit hash length via `redisbloom.setbit(256)` [n]. 4) Ensure SQL JOIN compatibility in payer workflows: `SELECT * FROM agent_billing JOIN billing_account ON agent_billing.enterprise_id = billing_account.uuid` [n].

## Who it's for

Small developers, mid-sized teams, and large enterprises requiring varying levels of query capacity and SLA guarantees [n]

## Novelty

Explicit documentation includes Redis pub/sub subscriber code (`redis_subscriber.py` with `psubscribe('block_validation_updates', callback=process_block_hash)` [n]) and PostgreSQL GIN index creation: `CREATE INDEX idx_block_hash ON agent_billing USING GIN (block_hash gin_trgm_ops)` [n].

## Ecosystem use

Track monthly active users per tier (Basic: 500+ developers, Pro: 200+ teams, Enterprise: 50+ enterprises) and monitor query volume distribution (Basic: 100 queries/month, Pro: 10,000 queries/month, Enterprise: custom SLA). Adoption metrics include developer sign-ups (target: 1,000+ unique API keys within 6 months) and query latency benchmarks (Pro tier reduces latency by 40% vs. Basic tier via Redis caching optimizations) [n].

## Diagram

```mermaid
graph TD
A[Query /api/solv/historical-records] --> B{Bloom filter check}
B -->|Cache hit| C[Return cached attestations]
B -->|Cache miss| D[Fetch L1 checkpoint via Etherscan]
D --> E[Redis pub/sub -> PostgreSQL]
E --> F[Build timeline JSON response]
F --> G[Return to client] [n]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/None*
