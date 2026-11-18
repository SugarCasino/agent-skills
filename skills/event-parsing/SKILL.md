---
name: event-parsing
description: Parse Sugar SDK events and raw generated numeric values. Use when decoding BetResultEvent.game_details, handling parseSugarEvent, parseGameEvent, parseGameDetails, PvP Coinflip event BCS helpers, SweetHouse redeem or referral claim events, or converting raw Move float and i64 values safely.
license: MIT
metadata:
  author: sugar
  version: '1.0.0'
  short-description: Parse Sugar events
  tags:
    - sugar
    - sui
    - sdk
    - events
---

# Sugar event parsing

Use this skill for application code that imports `@sugarcasino/sdk` and parses Sugar events or raw generated numeric structs. If the task is only SDK setup, use `installation` first. If the user is using `@sugarcasino/mcp` tool output, use `sugar-mcp` instead.

Use public SDK utilities and generated BCS helpers. Do not hand-decode `BetResultEvent.game_details` or copy SDK numeric struct guards into app code.

## Default workflow

1. Confirm the event source includes BCS bytes when payload decoding is needed.
2. Prefer `parseSugarEvent(event)` for supported game events.
3. Use generated BCS helpers for direct event parsing when the app needs specific event payloads.
4. Use `parseGameDetails({ game, gameDetails })` for manual `BetResultEvent.game_details` decoding.
5. Use raw Move numeric guards only for generated BCS values that did not come from SDK-normalized reads.
6. For parser and numeric conversion examples, read [references/events.md](references/events.md).

## Boundaries

- Standard game and PvP bet result events use the shared game-event parsing path.
- SweetHouse redeem events use `client.sugar.bcs.RedeemRequestCreatedEvent`; referral claim events use their matching generated BCS helpers in [the event reference](references/events.md).
- `client.sugar.getGameParameters()` already returns generated Move float fields as JavaScript numbers.
- Parsed `u64` and `u128` game detail values return `bigint` to preserve precision.
