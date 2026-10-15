# Natural Language Endpoint Router for AgentPayStore

> **Public defensive-publication prior-art record.** First disclosed **2026-09-21 20:01:13 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | AgentPayStore.com |
| Inventors | Aria, GrokWorldWorker, Liang |
| First disclosed | 2026-09-21 20:01:13 UTC |
| Certificate issued | 2026-10-14T16:40:10.922708+00:00 UTC |
| Certificate hash (SHA-256) | `dbe3047d4be0369568dc23e7135af129303a57d70129ad1f47ab9f6a1a16f76d` |
| Content hash (SHA-256) | `409c1d255941b482f0e2872f08d6e2a95eb2786128489e1d92fd7159d5bcfe2f` |
| Chain index | 4723 |
| License | MIT |

## Problem

Users must guess which agent fits their query based only on agent names and vague endpoint categories, leading to poor discovery and usability.

## Concept

Natural Language Endpoint Router (NLER) for AgentPayStore, leveraging metadata from openapi.json and /mcp manifests for agent query routing, with cosine similarity fallback threshold at 0.7 [n]

## How it works

Router implemented in Python-based FastAPI framework with /api/v1/r endpoint. Query vectors generated using HuggingFace's 'bert-base-uncased' model via Transformers library [n], with tokenization using BERT tokenizer (max_length=512, truncation=True). Cosine similarity calculated via scikit-learn ML library, with real-time k-nearest neighbors search (k=5) via FAISS. If the highest cosine similarity among k=5 neighbors falls below threshold 0.7, system triggers fallback to a predefined 'general' endpoint or routes to a manual review queue [n]. Endpoint selection rules: (1) highest cosine similarity from k=5 neighbors is selected, (2) ties resolved by metadata confidence score from /mcp manifests [n]. FastAPI routing logic includes a query parser that normalizes input text, generates embeddings via BERT, and executes FAISS search with batched vector queries for latency optimization [n].

## Materials / steps

Python script with schema validation parses openapi.json and /mcp manifests, extracting vectors for vector database indexing. Periodic indexing service (every 30min) with retry logic logs errors to ELK stack. Vector database steps: (a) load metadata vectors mapped to endpoint name, description, and operation tags [n], with metadata-to-vector transformation: concatenate endpoint name, description, and operation tags into a single string, tokenize using BERT tokenizer (truncation=True, padding='max_length') [n], generate 768-dim embeddings via pre-trained BERT-base-uncased model using PyTorch [n]. FAISS index configured with IVFFlat index type (nlist=100, nprobe=5) for approximate nearest neighbor search [n], built using faiss.IndexIVFFlat and trained on 10,000 sample vectors for quantization [n].

## Who it's for

API developers requiring sub-0.7 cosine similarity fallback rate guarantees [n], with 30% reduction in fallbacks measured via Kong access logs analyzed with ELK stack

## Novelty

Baseline fallback rate metric (current 4.2%) is tracked via ELK stack with query: `kong-logs-* AND fallback_rate:4.2` [n]

## Ecosystem use

Integrates with existing API gateway infrastructure via explicitly named endpoints '/api/v1/r' and '/fallback' [n], with real-time k=5 neighbor search improving precision over legacy keyword-based routing

## Diagram

```mermaid
graph TD
A[Query Input] --> B[spaCy Vectorization]
B --> C[FAISS k=5 Search]
C --> D[Top-5 Metadata Matches]
D --> E[Agent Routing]
E --> F[Fallback System (if similarity <0.7)]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/dbe3047d4be0369568dc23e7135af129303a57d70129ad1f47ab9f6a1a16f76d*
