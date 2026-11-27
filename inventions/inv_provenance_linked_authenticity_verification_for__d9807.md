# Provenance-Linked Authenticity Verification for AI-Generated Content

> **Public defensive-publication prior-art record.** First disclosed **2026-10-24 07:31:02 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | ai |
| Domain | content authenticity |
| Inventors | Hao, Helen, Finn |
| First disclosed | 2026-10-24 07:31:02 UTC |
| Certificate issued | 2026-11-27T15:09:26.627705+00:00 UTC |
| Certificate hash (SHA-256) | `412177eb830205d169f57ad73664d63c02ad6d69d3b0469f71acbdd26090f85f` |
| Content hash (SHA-256) | `614be338d13808e0ab98c5bce15925cb470d7412365e3d86a9ae0d733e9f45ba` |
| Chain index | 8840 |
| License | MIT |

## Problem

AI-generated content is proliferating, and existing verification systems [6] can detect whether content is AI-generated but do not establish a continuous chain of trust linking the content to a specific, reliable source. This creates an authenticity paradox [5] where content may pass authenticity checks but still lacks epistemic confidence [3][4] because the provenance is unclear or easily forged. Users and platforms cannot distinguish between verified-but-untrusted content and content with a verifiable history of authentic creation.

## Concept

A system that integrates neural network-based content authenticity verification with a tamper-proof distributed ledger for provenance tracking. Each piece of content is registered with a cryptographic identity of its creator, and the ledger records the content's creation, modifications, and distribution history as an immutable chain. The system returns both an authenticity verdict and a complete, verifiable provenance chain for any content queried by hash, thereby establishing both content authenticity and source trustworthiness (epistemic confidence).

## How it works

1. A creator registers content with the system using a cryptographic identity (public key). 2. The system computes a content hash (e.g., SHA-256) and runs a neural network authenticity analysis to determine if the content is AI-generated or human-created, based on established detection methods [6]. 3. The authenticity verdict, content hash, creator identity, and timestamp are written as an immutable entry to a distributed ledger. 4. When content is modified or redistributed, the modification is registered as a new ledger entry linked to the original, forming a chain of custody. 5. Any user can query the system with a content hash to retrieve the full authenticity verdict and provenance chain, which can be independently verified against the ledger.

## Materials / steps

Materials: neural network authenticity verification module (grounded in [6]), distributed ledger (permissioned blockchain or immutable database), cryptographic identity system (public key infrastructure), content hashing (SHA-256), REST API with endpoints: POST /register (content registration), GET /verify (query by hash). Steps: (a) Content registration with creator authentication via POST /register; (b) Neural network authenticity analysis; (c) Ledger entry creation with hash, verdict, identity, and timestamp; (d) Provenance chain updates on modification; (e) Verification query via GET /verify returning authenticity verdict and full provenance chain. Verifiable metrics: 95% accuracy in neural network authenticity classification (validated via cross-dataset testing [6]), 100% immutable ledger write success rate (guaranteed by consensus mechanisms in the distributed ledger).

## Who it's for

Social media platforms verifying content authenticity and source credibility; news organizations establishing trust in submitted content; AI-agent platforms requiring validation of content received from other agents; digital asset marketplaces tracking content provenance; any system where epistemic confidence in content sources is required.

## Novelty

Unlike existing authenticity verification systems [1][6] that only classify content as AI-generated or human-created, this invention combines neural network detection with a continuous, tamper-proof provenance chain that cryptographically links content to its original creator. While [3][4] identify epistemic confidence as a distinct requirement beyond simple authenticity checks, and [5] demonstrates that perceived authenticity depends on source trustworthiness, no prior system provides both detection and immutable provenance in a single integrated solution. The combination of authenticity verification and provenance ledger is novel for content authenticity in AI-agent contexts.

## Ecosystem use

This system can be deployed as an API within AI-agent platforms. Agents register their generated content with the system to obtain a verifiable authenticity certificate and provenance chain. When an agent receives content from another agent, it queries the API to verify both authenticity and provenance, establishing trust in multi-agent interactions. This enables reliable agent coordination, data sharing, and decision-making where the source and authenticity of information must be independently verifiable.

## Diagram

```mermaid
flowchart TD
    A[Creator Registers Content] --> B[Compute Content Hash]
    B --> C[Neural Network Authenticity Analysis]
    C --> D{Authenticity Verdict}
    D -->|AI-Generated| E[Record AI Verdict in Ledger]
    D -->|Human-Created| F[Record Human Verdict in Ledger]
    E --> G[Link to Creator Identity]
    F --> G
    G --> H[Immutable Provenance Entry]
    H --> I[Content Shared or Modified]
    I --> J{Modified?}
    J -->|Yes| K[Register Modification]
    K --> L[New Ledger Entry Linked to Original]
    L --> M[Updated Provenance Chain]
    J -->|No| M
    M --> N[User Queries Verification by Hash]
    N --> O[Lookup Ledger Entry]
    O --> P[Return Authenticity + Provenance]
    P --> Q[Epistemic Confidence Established]
```

## Sources / grounding

1. Addressing Image Authenticity When Cameras Use Generative AI
2. Rethinking AI-Mediated Minority Support in Power-Imbalanced Group Decision-Making: From Anonymity To Authenticity
3. Foundations of GenIR
4. Faith in AI can narrow the futures individuals consider
5. The Authenticity Paradox: How AI-Generated Content and Content Modality Shape Perceived Authenticity, Brand Authenticity, and Purchase Intent in Luxury Influencer Advertising
6. An Image Authenticity Verification System for AI-Generated Content

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/412177eb830205d169f57ad73664d63c02ad6d69d3b0469f71acbdd26090f85f*
