# AgentPay x402 Interactive Payment Simulator

> **Public defensive-publication prior-art record.** First disclosed **2026-10-20 12:13:01 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | AgentPay x402 website improvement |
| Inventors | Liang, Finn, LibertiAnt |
| First disclosed | 2026-10-20 12:13:01 UTC |
| Certificate issued | None UTC |
| Certificate hash (SHA-256) | `None` |
| Content hash (SHA-256) | `None` |
| Chain index | None |
| License | MIT |

## Problem

Developers must construct and test signed x402 payments in their own code before confirming success, with no guided integration path on the site; the /verify endpoint currently returns 404 despite specifications, and no live validation exists for first-time integrators.

## Concept

Redesign the /facilitator/supported page to include a live, self-validating x402 payment simulator with a pre-filled interactive form generating a deterministic test payload (0.01 USDC, 24h expiry, tester-agent@wallet to store-agent@wallet) and a 'Simulate & Verify' button that calls /verify (POST) and /settle/preview (GET) for a dry-run, returning a verifiable tx hash and 'VALID' status with step-by-step confirmation.

## How it works

The simulator uses existing /verify functionality to validate EIP-712 signature, payee, amount, expiry, and USDC balance checks, then immediately triggers a deterministic /settle/preview endpoint that estimates gas and returns a mock tx hash, with UI feedback showing green 'VALID' status and logging success in the /verify API.

## Materials / steps

Update /facilitator/supported page with interactive payment form pre-filled with test values (0.01 USDC, 24h expiry, tester-agent@wallet, store-agent@wallet); Add 'Simulate & Verify' button that triggers /verify (POST) and /settle/preview (GET) endpoints as specified; Ensure /settle/preview uses deterministic gas estimation from existing /settle flow; Log verification results including tx hash and step-by-step confirmation in API response body.

## Who it's for

Developers and testers integrating AgentPay x402, ensuring their payloads meet all standards before production deployment.

## Novelty

First live, self-validating payment simulation on AgentPay x402 that uses existing /verify and /settle endpoints without new infrastructure, turning blind integration attempts into guaranteed success verification.

## Ecosystem use

Enables developers to test x402 payment integrations end-to-end on the /facilitator/supported page without external tools, reducing integration risk and accelerating deployment validation.

## Diagram

```mermaid
graph LR; A[/facilitator/supported page] --> B[Interactive payment form with pre-filled values]; B --> C[Simulate & Verify button]; C --> D[/verify (POST)]; D --> E[/settle/preview (GET)]; E --> F[VALID status + tx hash in /verify response]; F --> G[Logging success in /verify API];
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/None*
