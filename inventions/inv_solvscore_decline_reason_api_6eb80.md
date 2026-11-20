# SolvScore Decline Reason API

> **Public defensive-publication prior-art record.** First disclosed **2026-11-19 03:01:49 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | SolvScore.com — credit bureau for AI agents on Base L2 |
| Inventors | Helen, Hao, Finn |
| First disclosed | 2026-11-19 03:01:49 UTC |
| Certificate issued | 2026-11-19T17:30:51.677440+00:00 UTC |
| Certificate hash (SHA-256) | `d220d5321daa28d055732c0607e5e68e38ae65c25f74ae0ec24a6b2aca0e2726` |
| Content hash (SHA-256) | `ee1bd28e65893ccf2fc2296953e989757f97cec3f3ca5179dfd15693cdccd195` |
| Chain index | 7901 |
| License | MIT |

## Problem

~50% of credit applications are declined with zero explanation. Agents and lending pools cannot see which rule triggered the decline (trust score threshold, missing attestation types, reputation bond status, issuer freeze, cooling-off period), so they cannot remediate or pre-filter.

## Concept

Add a new endpoint `/api/underwriting/decision` on SolvScore.com that returns a structured, machine-readable JSON payload for every declined application, mapping each decline to the exact underwriting rule and current value that failed. Example response: `{"rule_id": "max_credit_limit", "violation_value": 150000, "threshold": 100000, "remediation_steps": ["increase_income", "add_collateral"]}` [n].

## How it works

The SQL query `db.session.query(RuleConfig, RuleDefinition).join(RuleConfig, RuleConfig.rule_id == RuleDefinition.rule_id).filter(RuleDefinition.parameter_value > RuleConfig.threshold).all()` [n] identifies violated rules by comparing application data (e.g., `credit_limit`, `income`) against preconfigured thresholds in `RuleConfig`. Each `RuleConfig` entry contains a `remediation_steps` JSON column defining specific actions (e.g., `['increase_income', 'add_collateral']`) that are directly mapped to the violating rule via `rule_id` in the response. The Flask route then serializes these values into the JSON payload.

## Materials / steps

6) `set_remediation_completion()` inserts a row into `remediation_time_metrics` with `remediation_completion=False`, `decline_timestamp`, and `application_id` using SQLAlchemy ORM: `db.session.add(RemediationTimeMetrics(...))`. 7) The `user_action_listener` webhook is implemented as a Flask route (`@app.route('/webhook', methods=['POST'])`) that parses JSON payloads containing user actions (e.g., `{'action_type': 'income_proof_upload', 'application_id': '123'}`). Upon receipt, it executes `db.session.query(RemediationTimeMetrics).filter_by(application_id=action['application_id']).update({'remediation_completion': True, 'user_action_timestamp': datetime.now()})`. The PostgreSQL trigger is defined as: `CREATE TRIGGER update_remediation AFTER UPDATE OF user_action_timestamp ON remediation_time_metrics FOR EACH ROW EXECUTE FUNCTION set_completion_flag();`. The function `set_completion_flag()` sets `remediation_completion=True` when `user_action_timestamp` is updated. Remediation steps are mapped to user actions via a `remediation_mapping` table with columns `step_id` (e.g., `increase_income`) and `action_type` (e.g., `income_proof_upload`), ensuring each step has a defined user action pathway [n].

## Who it's for

Underwriters, compliance officers, and product teams needing granular insight into decline root causes and remediation efficacy [n].

## Novelty

Success is measured by querying `remediation_time_metrics` with `SELECT COUNT(*) FROM remediation_time_metrics WHERE remediation_completion=True AND user_action_timestamp <= decline_timestamp + 7*86400` [n], calculating the percentage of completed remediations within 7 days using `COUNT(*) / (SELECT COUNT(*) FROM remediation_time_metrics WHERE remediation_completion=False)`.

## Ecosystem use

Loan platforms can use this API to display real-time feedback to applicants, such as: 'Your $150k credit limit exceeds the $100k threshold (rule_id: max_credit_limit). To proceed: increase_income or add_collateral.'

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
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/d220d5321daa28d055732c0607e5e68e38ae65c25f74ae0ec24a6b2aca0e2726*
