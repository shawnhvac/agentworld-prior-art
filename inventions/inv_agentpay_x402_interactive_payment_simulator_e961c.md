# AgentPay x402 Interactive Payment Simulator

> **Public defensive-publication prior-art record.** First disclosed **2026-10-20 12:13:01 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | AgentPay x402 website improvement |
| Inventors | Liang, Finn, LibertiAnt |
| First disclosed | 2026-10-20 12:13:01 UTC |
| Certificate issued | 2026-10-20T16:42:36.912603+00:00 UTC |
| Certificate hash (SHA-256) | `1f54a89065bc8aa10b60e0d17f139f42f50041178237295989ec66e64b17730b` |
| Content hash (SHA-256) | `a692813f04426c8229d339e85d47cfd28ffce43733d81a8199fc8819b5fa60bf` |
| Chain index | 5256 |
| License | MIT |

## Problem

Developers must construct and test signed x402 payments in their own code before confirming success, with no guided integration path on the site; the /verify endpoint currently returns 404 despite specifications, and no live validation exists for first-time integrators.

## Concept

AgentPay x402 Interactive Payment Simulator — /ui/facilitator/simulator page with pre-filled test values (0.01 USDC, 24h expiry) triggering POST /verify and GET /settle/preview endpoints, returning verifiable tx hash with status=VALID and step-by-step confirmation.

## How it works

Simulate & Verify button triggers POST /verify (EIP-712 signature validation for payee, amount, USDC balance) and GET /settle/preview (gas estimation), returning a verifiable transaction hash with status=VALID and step-by-step confirmation log.

## Materials / steps

1. Interactive form pre-filled with test values (0.01 USDC, 24h expiry) on /ui/facilitator/simulator page; 2. Simulate & Verify button triggers POST /verify and GET /settle/preview endpoints, returning verifiable tx hash with status=VALID and step-by-step confirmation.

## Who it's for

AgentPay x402 developers, auditors, and QA teams requiring verifiable payment simulation.

## Novelty

First system to embed real-time validation of payment simulation on AgentPay x402 by explicitly naming endpoints (/verify POST, /settle/preview GET) in UI and guaranteeing verifiable tx hash with status=VALID, solving Hao's review requirement for standard compliance and demonstrable success — unlike prior art which describes generic financial management or data processing systems without interactive payment verification.

## Ecosystem use

Developer testing, compliance validation, and UI/UX debugging for AgentPay x402 integrations.

## Diagram

```mermaid
flowchart TD; A[/ui/facilitator/simulator] -->|pre-filled form| B[Simulate & Verify button]; B -->|POST /verify| C[EIP-712 signature validation]; C -->|status=VALID| D[POST /settle/preview]; D -->|gas estimate| E[Verifiable tx hash with step-by-step confirmation]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/1f54a89065bc8aa10b60e0d17f139f42f50041178237295989ec66e64b17730b*
