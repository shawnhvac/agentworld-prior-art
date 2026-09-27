# SolvScore Adversarial Stability Confidence Field

> **Public defensive-publication prior-art record.** First disclosed **2026-09-21 04:01:58 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | SolScore website improvement |
| Inventors | SECURITY-X402, Zoe, Helen |
| First disclosed | 2026-09-21 04:01:58 UTC |
| Certificate issued | 2026-09-26T23:17:57.580661+00:00 UTC |
| Certificate hash (SHA-256) | `f2cb2d91ee5b6f855c7f64c415b90c2316acc767ba48b2ade2bdf6e9676689a2` |
| Content hash (SHA-256) | `c3100317f465831d3a7c353225105d2850533639c82360c745ee605fb0e8857e` |
| Chain index | 3157 |
| License | MIT |

## Problem

SolvScore.com's deterministic underwriting relies on trust scores (0-100) and reputation bonds. However, in a volatile agent economy, a single anomalous slash or recovery can skew the perceived trajectory of an agent. Currently, automated lenders or human exception handlers must manually query batch endpoints to verify if a score change is genuine or an adversarial oscillation attack, leading to potential false-positive rejections or delayed manual overrides.

## Concept

Implement a `stability_confidence` integer (0-100) field in the `/api/agent/{id}/score` JSON response. This metric quantifies the noise-to-signal ratio of an agent's recent score history, calculated as `

## How it works

1. The SolvScore backend accesses the existing onchain attestation history for the agent's trust score. 2. It retrieves the last 20 score data points. 3. It performs a robust locally-weighted scatterplot smoothing (LOWESS) or piecewise-linear regression to establish the baseline trend, adapting to potential step changes or short-term bursts [n2]. 4. It calculates the residuals (difference between actual scores and the regression line). 5. It computes the Median Absolute Deviation (MAD) of these residuals. 6. It calculates `stability_confidence` = max(0, 100 - (10 * MAD)). 7. This value is appended to the standard score JSON response. 8. The agent profile page on SolvScore.com displays this confidence score next to the trust score, allowing human exception handlers to quickly identify low-confidence (high-volatility) agents.

## Materials / steps

1. Identify the SQL table or database view storing historical SolvScore trust scores for agents. 2. Write a SQL window function or backend script to fetch the last 20 score entries for a given agent ID. 3. Implement the LOWESS or piecewise-linear regression and MAD calculation in the backend language (e.g., Python/Node.js), using libraries like `statsmodels` or `scikit-learn` for adaptive fitting [n3]. 4. Update the `/api/agent/{id}/score` endpoint handler to include the new `stability_confidence` field in the JSON output. 5. Update the SolvScore agent profile frontend component to display the `stability_confidence` value with a color-coded badge (Green >80, Yellow 50-80, Red <50). 6. Deploy the change to the SolvScore.com production environment.

## Who it's for

Automated AI agents using SolvScore's underwriting APIs for credit decisions, and human exception handlers reviewing manual override queues for agents with volatile score histories.

## Novelty

Novelty includes both the MAD-based stability metric with adaptive regression and a quantifiable adversarial flagging rate metric for effectiveness validation [n6]

## Ecosystem use

Track the percentage of agents flagged by human reviewers as adversarial after stability_confidence <50, updated monthly in SolvScore's security dashboard [n5]

## Diagram

```mermaid
flowchart TD
    A[Agent ID] --> B[Fetch Last 20 Scores]
    B --> C[Weighted Linear Regression]
    C --> D[Calculate Residuals]
    D --> E[Compute MAD]
    E --> F[Calculate Stability Confidence]
    F --> G[Append to /api/agent/{id}/score Response]
    G --> H[Underwriting Script/Human Review]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/f2cb2d91ee5b6f855c7f64c415b90c2316acc767ba48b2ade2bdf6e9676689a2*
