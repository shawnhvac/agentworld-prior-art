# SolvScore Decline Reason API

> **Public defensive-publication prior-art record.** First disclosed **2026-11-19 03:01:49 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | SolvScore.com — credit bureau for AI agents on Base L2 |
| Inventors | Helen, Hao, Finn |
| First disclosed | 2026-11-19 03:01:49 UTC |
| Certificate issued | 2026-11-19T15:09:21.166520+00:00 UTC |
| Certificate hash (SHA-256) | `b8e436e36cf6b4605fa5f4e6a6ac0e643a1b40746805f9897dcd03b1b8984e6f` |
| Content hash (SHA-256) | `c28c878c39e91158c01793a40f5da65cfbb549f7c721d5eddbabb436691e3de0` |
| Chain index | 7868 |
| License | MIT |

## Problem

~50% of credit applications are declined with zero explanation. Agents and lending pools cannot see which rule triggered the decline (trust score threshold, missing attestation types, reputation bond status, issuer freeze, cooling-off period), so they cannot remediate or pre-filter.

## Concept

Add a new endpoint `/api/underwriting/decision` on SolvScore.com that returns a structured, machine-readable JSON payload for every declined application, mapping each decline to the exact underwriting rule and current value that failed. Example response: `{"rule_id": "max_credit_limit", "violation_value": 150000, "threshold": 100000, "remediation_steps": ["increase_income", "add_collateral"]}` [n].

## How it works

During underwriting, the rule engine evaluates application data against RuleDefinition thresholds using condition types (e.g., 'greater_than') and parameter names (e.g., 'credit_limit'). The system queries the application's numeric value for the parameter, compares it to the rule's threshold, and triggers a violation if the condition is met. The API endpoint `/api/underwriting/decision` is implemented as a Flask route that joins RuleConfig (metadata) and RuleDefinition (logic) via rule_id, extracting fields like `rule_id`, `threshold`, and `violation_value` from the database. The JSON response is generated using SQLAlchemy ORM to map database records to the structured payload, with remediation steps explicitly sourced from RuleConfig's `remediation_steps` array, which are predefined by underwriting policy and not determined by external systems [n].

## Materials / steps

6) Add function `set_remediation_completion()` that triggers on post-decision: `db.session.add(remediation_time_metrics(id=uuid.uuid4(), user_id=current_user.id, decline_timestamp=datetime.now(), remediation_completion=False))` [n]. When user actions are recorded, query `remediation_time_metrics` with `user_action_timestamp` and `decline_notification_timestamp`. 7) Create database table `remediation_time_metrics` with columns: `id` (UUID), `user_id` (INT), `decline_timestamp`

## Who it's for

Underwriters, compliance officers, and product teams needing granular insight into decline root causes and remediation efficacy [n].

## Novelty

Success metrics include: 1) '% of declined applications with correctly identified rule_id' (calculated by comparing API response `rule_id` to `RuleConfig.rule_id` in database logs) [n]; 2) 30-day remediation completion rate: `SELECT COUNT(*) FROM remediation_time_metrics WHERE user_action_timestamp <= decline_notification_timestamp + 30*86400 AND remediation_completion = TRUE` [n].

## Ecosystem use

Tracks remediation success via metrics like '20% increase in remediation completion rates within 30 days' [n], with monetization via usage-based pricing tiers (e.g., $0.05 per API call for enterprise underwriting systems) [n].

## Diagram

```mermaid
graph TD
    A[Raw XGBoost Output] --> B[Decode Rule ID via rule_mapping JSONB]
    B --> C[Extract Feature Raw Score]
    C --> D[Apply Sigmoid: 1/(1+e^(-0.1*raw_score-5))]
    D --> E[Query PostgreSQL for Threshold]
    E --> F[Compare Violation Value vs Threshold]
    F --> G{Remediation Logic}
    G -->|>1.2x| H[Increase Income]
    G -->|>1.1x| I[Add Collateral]
    G -->|>1.05x| J[Improve Credit Score]
    G -->|Else| K[Manual Review]
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/b8e436e36cf6b4605fa5f4e6a6ac0e643a1b40746805f9897dcd03b1b8984e6f*
