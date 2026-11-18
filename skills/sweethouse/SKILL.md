---
name: sweethouse
description: Build, scaffold, review, or fix Sugar SweetHouse public pool flows using @sugarcasino/sdk. Use when depositing into SweetHouse, minting staked coins, creating redeem requests from staked coins, claiming a delayed own redeem request, decoding SweetHouse redeem events, or keeping SweetHouse liquidity flows separate from standard game, PvP, referral, NFT, or MCP APIs.
license: MIT
metadata:
  author: sugar
  version: '1.0.0'
  short-description: Build SweetHouse pool flows
  tags:
    - sugar
    - sui
    - sdk
    - sweethouse
---

# Sugar SweetHouse

Use this skill for application code that imports `@sugarcasino/sdk` and builds SweetHouse public pool transactions. If the app has not configured the SDK client yet, use `installation` first. If the user is using `@sugarcasino/mcp` tools, use `sugar-mcp` instead.

Use `client.sugar.tx.sweetHouse`. Do not hand-write `core::sweethouse` Move calls, hard-code SweetHouse object ids, or route deposits and redeem requests through game bet builders.

## Default workflow

1. Confirm the client is extended with `sugar()` for the intended network.
2. Resolve supported coins and `objectIds.sweetHouse` from `client.sugar.getConfig()` when UI or diagnostics need them.
3. Build one of the `client.sugar.tx.sweetHouse` transactions.
4. Serialize only if the wallet or transport layer needs unsigned bytes.
5. Decode `client.sugar.bcs.RedeemRequestCreatedEvent` when the app needs the emitted redeem request id.
6. For action-specific inputs, examples, and event details, read [references/public-pool.md](references/public-pool.md).

## Transaction builders

The public pool has three transaction builders:

- `client.sugar.tx.sweetHouse.deposit(options)`
- `client.sugar.tx.sweetHouse.redeemRequest(options)`
- `client.sugar.tx.sweetHouse.claimOwnRedeemRequestAfterDelay(options)`

Read [references/public-pool.md](references/public-pool.md) for required inputs, examples, staked-coin behavior, and redeem event parsing.

## Gotchas

- Pass pool liquidity amounts as `amount`. The `stake` field belongs to game bets.
- Use base-unit `bigint` amounts once values leave the UI layer.
- Do not use `client.sugar.tx.createGameBet` for deposits, redeem requests, or delayed claims.
- Do not use referral or NFT builders for SweetHouse liquidity flows.
- Derive coin types and decimals from `client.sugar.getConfig().coins`; do not duplicate supported coin metadata unless the runtime config requires it.
- Derive `objectIds.sweetHouse` from SDK config. Do not hard-code network-specific SweetHouse ids in app code.

## Checklist

1. Confirm `sugar()` is registered on the Sui client.
2. Pick `deposit`, `redeemRequest`, or `claimOwnRedeemRequestAfterDelay` and read [references/public-pool.md](references/public-pool.md).
3. Pass `owner`, `coinType`, and the required `amount` or `requestId`.
4. Keep `useGasCoin` limited to deposits.
5. Submit or serialize through the app's normal unsigned transaction flow.
