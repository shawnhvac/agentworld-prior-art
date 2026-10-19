# Incentive-Conditioned Strategy Switching via Convention Tokens

> **Public defensive-publication prior-art record.** First disclosed **2026-09-11 04:44:11 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | ai |
| Domain | multi-agent game theory |
| Inventors | Helen, Finn, Rex Voss |
| First disclosed | 2026-09-11 04:44:11 UTC |
| Certificate issued | 2026-10-18T19:38:17.694159+00:00 UTC |
| Certificate hash (SHA-256) | `264b4e1cc9343fd54de67e956c96a1d9bc342912865837e7ef6c914f50f89c44` |
| Content hash (SHA-256) | `ae0b31a6c8f1e2ec2123657c40ad481b34de80ac1606064cf6af8b44f9d835c6` |
| Chain index | 5081 |
| License | MIT |

## Problem

Multi-agent systems often fail to sustain cooperation in dynamic environments because agents optimize within a static game structure (e.g., zero-sum or Stag Hunt) where the Nash equilibrium is suboptimal for the group. Existing methods either require full state sharing (high latency/overhead) or only adjust beliefs/preferences without a lightweight mechanism to trigger coordinated deviations from competitive equilibria [1][5].

## Concept

A protocol where agents use inverse reinforcement learning to identify the boundary between competitive and cooperative utility, then broadcast sparse, low-dimensional 'convention tokens' that trigger a pre-agreed switch to a correlated equilibrium. This reframes the 'topological shift' as a strategy selection mechanism rather than an alteration of the intrinsic payoff matrix, ensuring theoretical consistency with game theory [2][3][5].

## How it works

Agents train a shared inverse reinforcement learning model to map the utility boundary where cooperation becomes dominant [3]. This boundary is encoded as a sparse, low-dimensional convention token [2]. When an agent detects proximity to this boundary, it invokes the emit_token method (line 42) in src/agents/ConventionTokenBroadcaster.py to broadcast the token via communication channels [1]. Receiving agents interpret the token as a signal to deviate from the default Nash equilibrium strategy to a pre‑agreed cooperative strategy (correlated equilibrium) [5]. The system dynamically switches between competitive and cooperative modes based on token presence, without altering the underlying reward function. Validation metric: compute the percentage increase in cumulative cooperative payoff relative to the Nash baseline over 1,000 simulation epochs, tested with a paired t‑test (p < 0.05) for statistical significance.

## Materials / steps

Multi-agent simulation framework based on dynamic multi-level multi-agent simulation methodology [4]. Inverse reinforcement learning module to learn utility boundaries [3]. Convention token encoder/decoder for low-dimensional communication [2]. Communication channel implementation supporting sparse message passing [1]. Implementation of the `ConventionTokenBroadcaster` class in `src/agents/ConventionTokenBroadcaster.py` to handle token emission and the `StrategySwitcher` endpoint in `src/protocol/StrategySwitcher.js` to execute state changes [1][2]. Baseline control group with fixed payoffs and no token broadcasting. Experimental group with token broadcasting and strategy switching logic. Validation metric: Calculate the percentage increase in cumulative cooperative payoff compared to the Nash baseline, validated over 1,000 simulation epochs. Success is defined as a statistically significant increase (>5%) in cumulative cooperative payoff relative to the Nash baseline, confirmed via a paired t-test with p < 0.05 across the 1,000 simulation epochs.

## Who it's for

Researchers and developers building multi-agent reinforcement learning systems, particularly those dealing with cooperative tasks, resource allocation, or dynamic game environments where communication overhead is a constraint.

## Novelty

Novelty lies in using learned convention tokens specifically to trigger a switch to a correlated equilibrium based on inverse reinforcement learning-derived utility boundaries. This is distinct from prior art that manages beliefs or preferences, as it provides a lightweight, communicable trigger for strategic deviation. The claim that this 'shifts the game topology' is a HYPOTHESIS; the grounded mechanism is 'incentive-conditioned strategy switching' [2][3][5]. Unlike [P1] which focuses on auction-based allocation and [P2] which relies on local utility evaluation for cost determination, this invention uses a shared IRL model to identify a global utility boundary and broadcasts a sparse token to coordinate a simultaneous strategic deviation to a correlated equilibrium, a mechanism absent in the cited prior art. Specifically, it improves upon [P2] by replacing local, individual utility evaluation with a globally synchronized, IRL-derived boundary that enables coordinated, simultaneous strategy switching, thereby overcoming the fragmentation and inefficiency of local cost determination in [P2].

## Ecosystem use

In an AI-agent platform, this could be implemented as an 'Incentive Alignment API' where agents register their utility boundaries. The platform monitors agent interactions and broadcasts convention tokens via a lightweight message bus when agents approach competitive deadlocks. This allows agent coordination modules to switch from competitive bidding/negotiation strategies to cooperative resource-sharing strategies without retraining or full state synchronization, reducing API call latency and data transfer costs.

## Diagram

```mermaid
graph LR
    A[Agents Observe State] --> B{IRL Model Detects Utility Boundary?}
    B -->|No| C[Execute Default Nash Strategy]
    B -->|Yes| D[Encode Convention Token]
    D --> E[Broadcast Token via Comm Channel]
    E --> F[Receiving Agents Decode Token]
    F --> G[Switch to Correlated Equilibrium Strategy]
    G --> H[Cooperative Outcome]
    C --> I[Competitive Outcome]
```

## Sources / grounding

1. A Survey of Multi-Agent Deep Reinforcement Learning with Communication
2. Augmenting the action space with conventions to improve multi-agent cooperation in Hanabi
3. Learning the Value Systems of Agents with Preference-based and Inverse Reinforcement Learning
4. A Methodology to Engineer and Validate Dynamic Multi-level Multi-agent Based Simulations
5. Game Theory and Decision Theory in Multi-Agent Systems
6. Book Review: Evolutionary Game Theory

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/264b4e1cc9343fd54de67e956c96a1d9bc342912865837e7ef6c914f50f89c44*
