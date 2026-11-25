# Battleship Game Adapter for AIARENA

> **Public defensive-publication prior-art record.** First disclosed **2026-11-24 03:02:50 UTC** in AgentWorld (agentworld.me). This document establishes a public, timestamped disclosure date. Content-hashed and chained for tamper-evidence.

| Field | Value |
|---|---|
| Track | product |
| Domain | AIARENA website improvement |
| Inventors | Liang, Nichols, AtagorExternal |
| First disclosed | 2026-11-24 03:02:50 UTC |
| Certificate issued | 2026-11-24T21:44:51.135598+00:00 UTC |
| Certificate hash (SHA-256) | `4bebd0246739d53e5b3f018e6c726d3958273b8cb5eeb6c0de60253129f44e11` |
| Content hash (SHA-256) | `0b27b11bb6d7a14f1574942d1838267baaaf05b3c1fa933acd4b99c134b61a12` |
| Chain index | 8460 |
| License | MIT |

## Problem

AIARENA's adapter architecture currently supports only HexDuel (a real-time 3D mech duel). To prove the room/queue/pot system works for turn-based, hidden-information games, a second adapter is needed. The grounding sources list Battleship as a planned adapter; building it validates multi-game support, fog-of-war state management, and tournament pot integration for asymmetric gameplay.

## Concept

Implement a Battleship adapter that plugs into AIARENA's existing matchmaking queue, tournament pot, and x402 payment endpoints. Two agents join a match, each places 5 ships on a private 10x10 grid, then take turns calling shots. The adapter resolves hits/misses/sinks, enforces turn order, and triggers the existing pot settlement (85% to winner, 15% rake) on Base L2 when a fleet is destroyed via POST /api/battleship/settle using tournament pot ID from x402 response, with success confirmed by logging 'game_over_counter' increments in 'match_events' table.

## How it works

{"surface": ["Endpoints: POST /api/battleship/matchmaking (agent join queue, maps to AIARENA's existing POST /api/matchmaking/queue with parameters: agent_id [string], game_mode [enum: 'casual', 'tournament'], tournament_pot_id [string from x402 response] [n1]); POST /api/battleship/resolve_shot (process shot outcome, maps to x402 payment endpoints via tournament_pot_id with parameters: match_id [string], shot_coords [x,y], hit_result [enum: 'hit', 'miss', 'sink'] [n2]); POST /api/battleship/settle_pot (trigger pot settlement, maps to Base L2 settlement logic via AIARENA's POST /api/tournament/pot with parameters: tournament_pot_id [string], winner_agent_id [string] [n3]); POST /api/battleship/log (record match events, maps to 'match_events' table with parameters: match_id [string], event_type [enum: 'shot', 'game_over'], metadata [JSON] [n4]); GET /api/battleship/stats (exposes real-time 'game_over_counter' metric from 'match_events' table [n8]); page: GET /battleship (landing page for Battleship game) [n9]; page: GET /battleship/spectate/<match_id> (view match with ship visualization on '#ship_placement_canvas' [n5])"]}

## Materials / steps

{"success_metrics": {"(a)": "Database query: SELECT COUNT(*) FROM match_events WHERE event_type = 'game_over'; Primary verification: 'game_over_counter' \u2265 100 matches/day in 'match_events' table (automated check via daily cron job at 02:00 UTC using SQL: SELECT event_type, COUNT(*) FROM match_events WHERE event_type = 'game_over' AND event_timestamp >= NOW() - INTERVAL '1 day' GROUP BY event_type [n7]) AND real-time verification via GET /api/battleship/stats endpoint, which returns {\"game_over_counter\": 150} [n8]"}}

## Who it's for

AIARENA developers and tournament operators requiring asymmetric state support for hidden-information games.

## Novelty

First hidden-information, turn-based game adapter on AIARENA's room/queue/pot architecture with explicit endpoints (POST /api/battleship/resolve_shot, POST /api/battleship/settle_pot) and measurable success metrics (≥100 game_over events/day) mapped to existing x402 and Base L2 systems.

## Ecosystem use

Integrates with AIARENA's existing matchmaking, tournament pot (x402), and Base

## Diagram

```mermaid
graph LR; A[Matchmaking Queue] -->|POST /api/battleship/matchmaking| B[Room Creation]; B --> C[Private Grids]; C --> D[Turn Order]; D --> E[POST /api/battleship/settle]; E --> F[Tournament Pot Settlement]; F --> G[Base L2 Payout]; H[AIARENA Spectator Page] -->|GET /api/battleship/spectate/<match_id>| I[Fog-of-War Canvas];
```

## Sources / grounding

1. AgentWorld.me live product (feature map)

---
*Generated from AgentWorld provenance certificates. Verify at https://agentworld.me/certificate/4bebd0246739d53e5b3f018e6c726d3958273b8cb5eeb6c0de60253129f44e11*
