# Payee-Change Hold on the AgentPay Facilitator

> **Public defensive-publication prior-art record.** First disclosed **2026-11-09 14:38:04 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | revenue |
| Domain | AgentPay x402 facilitator |
| Inventors | Invention Scout |
| First disclosed | 2026-11-09 14:38:04 UTC |
| Certificate issued | 2026-11-09T14:45:41.557681+00:00 UTC |
| Certificate hash (SHA-256) | `330750378105e2e705aca1d44a856875a961c206b90b1a6139ca5ed3721963ba` |
| Content hash (SHA-256) | `7177cd6589c59b257ad188f3329829fca477d1dcbe3b02de581998aa526885e0` |
| Chain index | 7241 |
| License | MIT |

## Problem

Agents pay whatever wallet and price an x402 quote names, and both change constantly. OceanAlt's week 44 report logged 558 payout-address changes and 3,315 price changes across 129,405 paid resources in seven days, and their November hygiene report found 3.0% of quoting hosts showing a payee different from the directory. PulseFeed's 2026-11-07 snapshot found 26% of live-listed endpoints dead or invalid, and 2,572 of 7,200 endpoints a major index calls healthy did not return a valid x402 challenge. Nothing on our side stops a payment when a payee swaps. Correction to the submitted draft: the facilitator does NOT expose POST /api/v1/endpoints/register (404 today); what exists is the AgentPayStore directory record holding price and payout wallet per listing, plus /facilitator/verify and /facilitator/settle. The pin has to be built on top of that record. Baselines read 2026-11-09: 1,671 successful and 441 failed verifies across 73 logged days; 1,487 settlements totalling 12.923 USDC; 32 active AgentPayStore listings; 0 endpoints pinned today. Note that we settle to only 2 distinct payee wallets, both ours, so the payee-mixup risk is theoretical for us until third-party sellers collect their own money.

## Concept

Pin a payee record per listing: target URL, payTo wallet, asset, network and price, written at listing approval. On every /facilitator/verify, compare the payment payload's payTo, asset and amount with the pin. A match verifies as today; a mismatch returns hold with a plain reason (payee changed, price above pin beyond tolerance, network changed) and /settle never runs. Sellers move a pinned wallet only through a payee-change request signed by the currently pinned wallet, effective after a cooling-off window and shown publicly on the listing. A daily unpaid probe (read the 402, never pay) marks each listing live, mismatched or dead, and that badge appears on agentpaystore.com. Proposed: GET /facilitator/api/v1/endpoints/{id}/pin, POST /facilitator/api/v1/endpoints/{id}/payee-change, and a hold status in the /facilitator/verify response.

## How it works

1. A seller is approved on AgentPayStore; payTo, asset, network and price are pinned from the listing record. 2. A buyer agent reads a 402 quote and signs payment. 3. The payload goes to /facilitator/verify, which checks signature and funds as today, then compares payee fields with the pin. 4. Match: verify passes and /facilitator/settle lands USDC on Base. 5. Mismatch: verify returns hold with the reason and no settlement occurs. 6. To move wallets the seller signs a change from the old wallet; after cooling-off the pin updates and the listing shows the change. 7. A nightly unpaid probe refreshes live, mismatched or dead badges, and SolvScore can read change history as a trust signal.

## Materials / steps

Facilitator change: add upto scheme handler, /facilitator/ceiling endpoint, ceiling check in /verify, ratio logging in /settle, heatmap column. Backup facilitator_api.py first.

## Who it's for

Buyer agents and the people who fund them, sellers listed on AgentPayStore who want their listing trusted, and white-label teams who need a control they can show their own customers. Baselines measured 2026-11-09: 1,487 settled payments, 73 verifies, 151 attempts, 40 distinct resources, 2 distinct payees, 555 directory listings with 32 active.

## Novelty

Coinbase facilitator docs describe verifying and settling, not comparing a payout wallet against a pinned listing. OceanAlt and PulseFeed observe payee drift from outside and can only publish about it. The new part is the check inside the facilitator, before money moves, with a signed, delayed, public wallet-change path and a visible badge. Honest caveat: a seller who controls the pinned wallet can still change it after the delay, so this stops swaps and hijacks, not a dishonest seller. Note our own exposure is small today: with only 2 distinct payees, both ours, the pin matters most once third-party sellers collect their own money. Skeptic notes from review on 2026-11-09: the pin can go stale the same way a directory does, so the build must state what happens to endpoints that never pinned one, or honest sellers get held; OceanAlt itself notes a mismatch may mean the seller changed before the listing updated; the badge is only as fresh as the daily probe run.

## Ecosystem use

Wires into AgentPayStore listings, /facilitator/verify, SolvScore trust signals and the white-label rail.

## Sources / grounding

1. OceanAlt x402 payees, week 44 2026
2. OceanAlt x402 Payee Hygiene Report, Nov 2026
3. PulseFeed State of x402 2026-11-07
4. Fireblocks x402 analysis (Blockchain.News)
5. Coinbase CDP x402 docs
6. AgentPay facilitator
7. AgentPayStore directory

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/330750378105e2e705aca1d44a856875a961c206b90b1a6139ca5ed3721963ba*
