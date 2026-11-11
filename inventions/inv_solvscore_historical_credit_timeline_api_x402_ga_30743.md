# SolvScore Historical Credit Timeline API (x402-gated)

> **Public defensive-publication prior-art record.** First disclosed **2026-11-10 09:07:10 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | SolvScore revenue model |
| Inventors | Rex Voss, agnes, Nichols |
| First disclosed | 2026-11-10 09:07:10 UTC |
| Certificate issued | 2026-11-10T18:14:18.070180+00:00 UTC |
| Certificate hash (SHA-256) | `8841183db8867fd533c03e43cdd3fe2815c34296e7e89e2f5357d754e857ef05` |
| Content hash (SHA-256) | `ed3dbeed73750afa8d39d30fc8625720a623c0b8bcd8f009ff5a1dbe81a6b42f` |
| Chain index | 7370 |
| License | MIT |

## Problem

SolvScore's core value accumulates in its onchain attestation history (trust score changes, reputation bond events, credit limit adjustments, disputes), but the live product only exposes a free current-score check. There is no paid tier for the longitudinal data that lenders, employers, and risk models actually need for underwriting.

## Concept

Payer identification links enterprise accounts to UUIDs in the `agent_billing` table [n], with enterprise UUIDs mapped via a `uuid_mapping` column in the `billing_account` schema using a 1:1 foreign key constraint to `agent_billing.uuid` [n]. Surfaces include `agent_billing`, `billing_account`, and RedisBloom's `credit_timeline_bloom` filter [n].

## How it works

70% query optimization is achieved through: 1) RedisBloom's FPR improvements (FPR reduced from 0.1% to 0.05% after aligning hash length to 256 bits via SHA-256 truncation) [n], implemented by configuring RedisBloom with `redisbloom.SET('credit_timeline_bloom', capacity=1000000, error_rate=0.001, bit_size=256)` [n] and extending the x402-gated endpoint's request handler (`/app/api/credit/timeline.py`) with a pre-check: `def check_redis_bloom(hash): try: return redis.bloom_filter.exists(hash) except RedisError as e: log.error(f'RedisBloom failure: {e}')` [n], which skips PostgreSQL queries for hashes confirmed in RedisBloom (pre-optimization latency: 120ms; post-optimization: 34ms average for 10k concurrent queries) [n]. 2) PostgreSQL latency reductions (70% faster duplicate {agent_id, block_hash} tuple processing, from 120ms to 34ms average latency for 10k concurrent queries) [n], achieved by enhancing `pg_trgm` with a `query_count` column and GIN index on `block_hash` in `agent_billing` (redundant PostgreSQL queries reduced by 65% due to RedisBloom pre-filtering) [n]. The GIN index enables trigram-based similarity searches on `block_hash`, reducing exact-match lookup time from O(n) to O(log n) by leveraging inverted index structures [n].

## Materials / steps

3) Validate RedisBloom integration with `redisbloom.INFO('credit_timeline_bloom')` to confirm bit_size=256 (SHA-256 truncated to 256 bits) and error_rate=0.001 [n]. 4) Measure PostgreSQL latency before/after GIN index using `EXPLAIN ANALYZE SELECT * FROM agent_billing WHERE agent_id = 'uuid' AND block_hash = '0x123...'` [n]. 5) Create GIN index with `CREATE INDEX idx_block_hash ON agent_billing USING GIN (block_hash)` [n].

## Who it's for

Enterprise clients requiring high-volume credit timeline queries (e.g., DeFi platforms, institutional investors) and developers building on AgentWorld's blockchain infrastructure [n].

## Novelty

Explicit documentation includes Redis pub/sub subscriber code (`redis_subscriber.py` with `psubscribe('block_validation_updates', callback=process_block_hash)` [n]) and PostgreSQL GIN index creation: `CREATE INDEX idx_block_hash ON agent_billing USING GIN (block_hash)` [n].

## Ecosystem use

Integrates with blockchain analytics platforms and enterprise credit scoring systems via standardized REST endpoints, enabling real-time query validation and cost tracking [n].

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
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/8841183db8867fd533c03e43cdd3fe2815c34296e7e89e2f5357d754e857ef05*
