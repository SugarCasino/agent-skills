---
name: create-pvp-games
description: Build, scaffold, review, or fix Sugar PvP game flows using @sugarcasino/sdk. Use when selecting the correct PvP game specification, designing shared PvP lobby or event-handling architecture, or adding a supported PvP game flow. For PvP Coinflip create, join, cancel, lobby, parameter, object, or event work, read references/pvp-coinflip.md.
license: MIT
metadata:
  author: sugar
  version: '1.0.0'
  short-description: Build Sugar PvP game flows
  tags:
    - sugar
    - sui
    - sdk
    - pvp-games
---

# Create Sugar PvP games

Use this skill for application code that imports `@sugarcasino/sdk` and works with PvP games. If the user is using MCP tools instead, use `sugar-mcp`.

## Workflow

1. Identify the exact PvP game before choosing SDK builders, parameters, objects, or events.
2. For PvP Coinflip, read [the PvP Coinflip specification](references/pvp-coinflip.md).
3. For another PvP game, use its dedicated specification when available; do not adapt the coinflip builder, input shape, registry, or events.
4. Share lobby state, wallet ownership, coin metadata, serialization, and event display across the UI where useful. Keep the transaction rules specific to each game.

## Current specification

- `pvp-coinflip`: create, join, cancel, unresolved-lobby reads, parameter reads, object reads, and event decoding. Read `references/pvp-coinflip.md`.

## Boundaries

Optional PvP transaction fields accept explicit `undefined`, including with `exactOptionalPropertyTypes`. This has the same effect as omitting the option.

- Treat each PvP game as a separate on-chain flow with its own inputs, builders, state lifecycle, and events.
- Do not route PvP game transactions through `createGameBet`, which is only for standard games.
- Use `client.sugar.getConfig().coins` for supported coin metadata and keep wallet addresses consistent as `owner`.
- Decode Move payloads with generated BCS helpers and game-specific SDK parsers.
