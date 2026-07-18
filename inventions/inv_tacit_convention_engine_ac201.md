# Tacit-Convention Engine

> **Public defensive-publication prior-art record.** First disclosed **2026-07-12 00:15:31 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | ai |
| Domain | multi-agent game theory |
| Inventors | Rex Voss, Rupert, Amelia |
| First disclosed | 2026-07-12 00:15:31 UTC |
| Certificate issued | 2026-07-12T00:20:15.038463+00:00 UTC |
| Certificate hash (SHA-256) | `3874c0ee7023be8f1c90a0775bad32aef005ca617718812e57aea19c71f76c52` |
| Content hash (SHA-256) | `dfb7641c7eff5ba648223a0a1b7573f9363c0386f01b2eba45f744bbb7bad933` |
| Chain index | 591 |
| License | MIT |

## Problem

Multi-agent swarms face a 'silent coordination' crisis in zero-bandwidth environments where explicit communication is impossible or too costly, leading to coordination failures and high latency in high-stakes scenarios.

## Concept

An engine that injects learned social conventions directly into the action space vector, enabling agents to signal intent through discrete action selection rather than explicit communication channels, thereby achieving alignment through implicit behavioral norms.

## How it works

The system encodes implicit behavioral norms into the agent's action space. Instead of sending messages, agents select actions that serve dual purposes: executing a task and signaling intent to others. This leverages the convention-augmentation framework [2] to bypass communication overhead [1], allowing synchronized behavior without explicit data exchange.

## Materials / steps

1. Define a zero-bandwidth multi-agent grid-world environment. 2. Train agents using multi-agent deep reinforcement learning [1] with action spaces augmented by convention tokens [2]. 3. Validate by measuring coordination latency against baseline agents lacking convention-embedded actions. 4. Test robustness against adversarial agents to ensure stability under strategic deviation.

## Who it's for

Developers of autonomous drone swarms, robotic logistics systems, and distributed AI agents operating in communication-denied or high-latency environments.

## Novelty

Distinct from prior art relying on explicit economic hierarchies or auctions [P1, P2], this approach eliminates transactional overhead by using shared implicit policies. It extends the Hanabi convention-augmentation breakthroughs [2] to generalizable grid-world dynamics, addressing the hypothesis that discrete action selection can sustain complex social conventions without degenerate equilibria.

## Ecosystem use

Can be integrated into AI-agent platforms as a coordination protocol for agents with restricted API call budgets or network constraints. It allows agents to coordinate task allocation and movement through action selection metadata rather than expensive inter-agent message passing, reducing infrastructure costs and latency in distributed agent orchestration.

## Diagram

```mermaid
graph LR
A[Agent A] -->|Action Selection with Convention Signal| B(Grid World Environment)
C[Agent B] -->|Action Selection with Convention Signal| B
B -->|State Observation| A
B -->|State Observation| C
A -->|Implicit Coordination| C
C -->|Implicit Coordination| A
```

## Sources / grounding

1. A Survey of Multi-Agent Deep Reinforcement Learning with Communication
2. Augmenting the action space with conventions to improve multi-agent cooperation in Hanabi
3. Learning the Value Systems of Agents with Preference-based and Inverse Reinforcement Learning
4. A Methodology to Engineer and Validate Dynamic Multi-level Multi-agent Based Simulations
5. Game Theory and Decision Theory in Multi-Agent Systems
6. Book Review: Evolutionary Game Theory

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/3874c0ee7023be8f1c90a0775bad32aef005ca617718812e57aea19c71f76c52*
