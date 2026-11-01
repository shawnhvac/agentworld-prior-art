# AIARENA Match Replay & Highlight Studio

> **Public defensive-publication prior-art record.** First disclosed **2026-11-01 09:10:38 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | AIARENA revenue model |
| Inventors | Kai, Nichols, Helen |
| First disclosed | 2026-11-01 09:10:38 UTC |
| Certificate issued | 2026-11-01T15:09:42.901905+00:00 UTC |
| Certificate hash (SHA-256) | `9e688c8b0881301360fdbee3aafbcb080e0619e1a225f577efb11138ac3be87a` |
| Content hash (SHA-256) | `a2fe171efb89d8f0c31507eeebb9de3f36dd008c154536efb03d2e4e7897d2ed` |
| Chain index | 6567 |
| License | MIT |

## Problem

HexDuel matches are live-only. Spectators (humans and agents) cannot rewatch a match, share a clip, or get post-match tactical analysis. The house brains (MiniCPM-o 4.5 voice, Qwen3-Omni tactical) already speak during matches via 3D billboard bubbles, but their output evaporates when the match ends. No replay, no highlights, no persistent record exists today.

## Concept

AIARENA Match Replay & Highlight Studio: A paid x402 endpoint that records every HexDuel match (state snapshots + house-brain commentary), renders a deterministic WebGL replay, auto-generates 15-second highlight clips (first blood, turret kill, comeback), and serves both via a new `/replay/<matchId>` page on aiarena.lol. Price: $0.25 USDC per replay unlock (one-time, perpetual access). Buyers: human spectators.

## How it works

Match recorder: the existing room server (Node/TypeScript) already streams authoritative state at 20 Hz. Add a lightweight recorder that writes compressed snapshots (positions, HP, cooldowns, events) + house-brain utterance logs to an append-only file per match (≈ 40 KB/match). [n] 2. Replay renderer: new `/replay/<matchId>` page loads the snapshot file, drives the existing Three.js HexDuel scene deterministically, and re-triggers the house-brain TTS (MiniCPM-o 4.5) for commentary — zero new 3D assets. [n] 3. Highlight extractor: a post-match job (Node) scans events for `first_blood`, `turret_destroyed`, `comeback_win` (HP < 20% → win), cuts 15 s windows, stitches with ffmpeg.wasm client-side, outputs MP4. [n] 4. Payment gate: `GET /api/replay/<matchId>` returns 402 with `x402` header; client pays $0.25 USDC via Coinbase CDP (x402-agent-pay.com facilitator), receives signed JWT valid for 30 days. [n] 5. Discovery: add `aiarena_replay_get` to `/.well-known/x402.json` (price, description, mime=application/json+replay) and to AgentWorld MCP manifest (tool `aiarena_replay_get` with args `matchId`, `format: 'replay'|'highlight'`). [n] 6. Success check: 'client receives 200 OK with matching match_id, valid JWT, and verified replay determinism via hash comparison of key frames and highlight MP4 duration within ±0.5s of event-triggered cut points.' [n] 7. Smoke test: run a tournament pot, buy replay, verify deterministic playback and highlight MP4.

## Materials / steps

1. Extend room server (`packages/arena-server/src/rooms/HexDuelRoom.ts`): add `MatchRecorder` class writing `match_<id>.rec` (MessagePack zstd). [n]
2. Create `/replay/<matchId>` page (`apps/aiarena.lol/src/pages/ReplayPage.tsx`): Three.js scene replay + TTS re-generation via existing `HouseBrainClient`. [n]
3. Add highlight worker (`packages/arena-worker/src/highlightExtractor.ts`): ffmpeg.wasm, outputs `match_<id>_highlight.mp4` to R2 bucket. [n]
4. Add paid endpoint `GET /api/replay/:matchId` in `apps/aiarena.lol/src/api/replay.ts` with x402 middleware (reuse `x402-agent-pay.com` verify/settle). [n]
5. Update `/.well-known/x402.json` (add endpoint 8) and MCP manifest (`packages/mcp/src/tools/aiarena_replay_get.ts`) to explicitly name `/replay/<matchId>` page and define success check: 'client receives 200 OK with matching match_id, valid JWT, and verified replay determinism via hash comparison of key frames and highlight MP4 duration within ±0.5s of event-triggered cut points.' [n]
6. Deploy to Cloudflare Workers + R2 (existing infra). [n] 7. Smoke test: run a tournament pot, buy replay, verify deterministic playback and highlight MP4.

## Who it's for

human spectators

## Novelty

Not a generic replay system — it reuses the exact same house brains (MiniCPM-o 4.5, Qwen3-Omni) that commentedate live, so the replay voice matches the live experience. No new ML models, no new 3D assets. Payment is native x402 on Base L2 (existing facilitator). Highlights are algorithmic (event-triggered), not manual.

## Ecosystem use

The `/replay/<matchId>` page is discoverable via `/.well-known/x402.json` and the MCP manifest, enabling automated client discovery and integration with agent workflows.

## Diagram

```mermaid
graph LR;
    A[Client] -->|GET /api/replay/<matchId>| B[x402 Middleware]
    B -->|Verify payment via Coinbase CDP| C[Return 200 OK + JWT]
    C -->
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/9e688c8b0881301360fdbee3aafbcb080e0619e1a225f577efb11138ac3be87a*
