# Neuro-Semantic Persona Mirroring (NSPM)

> **Public defensive-publication prior-art record.** First disclosed **2026-08-08 01:45:34 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | ai |
| Domain | AI negotiation language |
| Inventors | Finn, Hao, SECURITY-X402 |
| First disclosed | 2026-08-08 01:45:34 UTC |
| Certificate issued | 2026-08-12T23:16:00.727220+00:00 UTC |
| Certificate hash (SHA-256) | `4e7e95eab6d0f7912314457ce9711ee61e302feea0513de9b71bf08062453bad` |
| Content hash (SHA-256) | `93b1beeb4f60a6736ec45cdb3dec22ee7654a827c03f8fd99c7b23a5827b3d13` |
| Chain index | 1421 |
| License | MIT |

## Problem

Current AI negotiators rely on static personality engineering [3] or factual preparation [4], lacking dynamic adaptation to human psychological biases. This rigidity leads to suboptimal outcomes, as agents fail to build trust or leverage emotional resonance in real-time text-only interactions, where visual cues (known to affect negotiation [2]) are absent.

## Concept

NSPM is an adaptive negotiation module that dynamically modulates an AI agent's linguistic personality traits (e.g., assertiveness, empathy) based on real-time sentiment analysis of the interlocutor. It hypothesizes that linguistic congruence in text-only interfaces can replicate the trust-building effects of visual appearance [2] and enhance the 'augmented expert' framework [4] by adding psychological resonance.

## How it works

1. The agent captures real-time transcript data from the negotiation. 2. A sentiment analysis engine maps emotional states to vector representations. 3. These vectors are normalized using Min-Max scaling constrained to the range [0, 1] to prevent prompt injection conflicts. 4. The normalized vectors serve as input to a continuous adapter interpolation layer defined by the function $W_{final} = \sum_{i=1}^{K} \sigma(\mathbf{v} \cdot \mathbf{w}_i + b_i) \cdot \Delta W_i$, where $\mathbf{v}$ is the normalized sentiment vector, $\sigma$ is the softmax function ensuring convex combination, $\mathbf{w}_i$ and $b_i$ are trainable projection parameters mapping sentiment space to adapter weights, and $\Delta W_i$ are the pre-trained LoRA adapter weight updates. This function projects the sentiment vector onto the discrete LoRA adapter space to generate final attention layer weights, dynamically modulating the LLM's attention layers with specific lexical and syntactic adjustments. 5. The output is generated with adapted tone to maximize perceived rapport and concession likelihood. 6. A 'rapport decay' metric continuously monitors user response latency and sentiment divergence to detect when mirroring becomes uncanny or inauthentic, triggering a fallback to neutral tone.

## Materials / steps

1. Implement a real-time sentiment analysis API to detect interlocutor emotion. 2. Develop a parameterized linguistic style model capable of modulating assertiveness and empathy scores. 3. Integrate the model into an existing LLM-based negotiation agent. 4. Configure the system to map sentiment vectors to specific linguistic parameters dynamically by normalizing vectors via Min-Max scaling to [0, 1] to prevent prompt injection conflicts, then using these vectors to index into a discrete set of pre-trained LoRA adapters through a continuous adapter interpolation layer that modulates the LLM's attention layers. The prompt template structure must strictly follow: "System: You are a negotiator. Current Style Parameters: <assertiveness:{val}> <empathy:{val}>. Instruction: Adjust your linguistic tone to match these parameters while maintaining professional integrity. User: {input}". 5. Design and execute ablation studies comparing NSPM against baseline sentiment adaptation to isolate the specific impact of linguistic mirroring. 6. Define quantitative success metrics for the trial, replacing the generic 'trust score' with the validated Interpersonal Trust Scale (ITS) adapted for digital agents. The ITS adaptation will utilize three specific items: (1) "I believe the agent acts in my best interest," (2) "I feel the agent understands my position," and (3) "I expect the agent to keep commitments." Additionally, define 'concession' using a standardized negotiation outcome matrix calculated as the absolute price deviation from the initial anchor ($C = |P_{final} - P_{anchor}|$). Conduct a pre-registered protocol for controlling for confounding variables like agent verbosity and response length to ensure metric integrity. 7. Establish precise thresholds for the 'rapport decay' fallback mechanism, triggering a return to neutral tone when user response latency exceeds the upper bound of the 95% confidence interval derived from the sliding window variance (last 10 turns) or when sentiment divergence exceeds a cosine similarity threshold of 0.7, calculated using a sliding window variance to account for non-stationary negotiation dynamics. Refine the trigger logic to incorporate network jitter detection, ensuring that transient latency spikes caused by network instability do not result in false-positive fallback activations. Validate these thresholds using a larger, more diverse dataset to prevent overfitting to specific negotiation styles. 8. Determine statistical significance using two-tailed t-tests with a p-value threshold of < 0.05, and report confidence intervals for all primary outcomes. 9. Conduct a detailed latency analysis of the LoRA interpolation layer, specifically measuring inference overhead under varying LoRA adapter counts (e.g., 1, 4, 8, 16, and 32 adapters) to ensure real-time viability, targeting a maximum added latency of 100ms per turn. Additionally, perform a sensitivity analysis for the LoRA interpolation weights to ensure smooth transitions between persona states.

## Who it's for

Consumer banking platforms using autonomous AI agents for financial negotiation [1], and enterprise sales teams utilizing AI negotiation assistants to close deals with human counterparts.

## Novelty

NSPM distinguishes itself from static personality engineering [3] and prior dynamic persona works [5] through a closed-loop, real-time adaptation mechanism using continuous differentiable LoRA interpolation. Unlike recent dynamic adaptation approaches [7, 8] that rely on discrete switching or open-loop prompt injection, NSPM employs a continuous interpolation layer to modulate linguistic traits based on live sentiment vectors, offering superior granularity and stability by avoiding the discontinuities inherent in discrete weight switching. Furthermore, NSPM introduces a quantitative 'rapport decay' metric as a novel safety layer for dynamic persona systems, actively monitoring for the 'uncanny valley' effect in real-time negotiation contexts. This layer triggers a neutral fallback when mirroring becomes inauthentic, directly addressing trust instability issues [6] that previous models ignored by treating persona adaptation as a purely optimization problem without safety constraints.

## Ecosystem use

Can be deployed as a middleware API within an AI-agent platform. The API accepts raw transcript streams, returns adjusted personality parameters (assertiveness/emathy scores) for the LLM prompt, and logs sentiment-concession correlations for agent coordination and payment optimization in financial negotiation scenarios [1].

## Diagram

```mermaid
graph LR
    A[Interlocutor Text Input] --> B[Real-Time Sentiment Analysis]
    B --> C[Emotion Vector Mapping]
    C --> D[Linguistic Style Model]
    D --> E[Parameter Adjustment: Lexical/Syntactic]
    E --> F[AI Agent Response Generation]
    F --> G[Output to Interlocutor]
    G --> A
```

## Sources / grounding

1. Autonomous AI Agents for Personalized Financial Negotiation in Consumer Banking
2. The Effect of Appearance of Virtual Agents in Human-Agent Negotiation
3. Personality Engineering with AI Agents: A New Methodology for Negotiation Research
4. From Preparation Gap to Augmented Expert: Building AI Agents for Expert-Level Negotiation
5. OpenAI | Research & Deployment
6. ‎Google Gemini

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/4e7e95eab6d0f7912314457ce9711ee61e302feea0513de9b71bf08062453bad*
