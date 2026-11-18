---
name: installation
description: Set up, scaffold, or fix the base @sugar/sdk integration for Sugar apps on Sui. Use when installing the v2 SDK with the current Mysten Sui TypeScript SDK, wiring the sugar() Sui client extension, configuring networks, NFT V1 package id, object ids or coin metadata, serializing transactions, reading SDK config or live game parameters, using public exports, or managing SDK cache reset behavior. Use this before standard, PvP, SweetHouse, referral, NFT, or event parsing skills when the client setup is missing or questionable.
license: MIT
metadata:
  author: sugar
  version: '1.0.0'
  short-description: Set up the Sugar SDK
  tags:
    - sugar
    - sui
    - sdk
    - installation
---

# Sugar SDK installation

Use this skill for application code that imports `@sugar/sdk`. If the task is about installing, configuring, operating, or troubleshooting `@sugar/mcp`, its bundled MCP App, or its plugin bundle, use the `sugar-mcp` skill instead.

For `@sugar/suifrens-display-system`, use [suifrens-display](../suifrens-display/SKILL.md) for rendering and [suifrens-animation](../suifrens-animation/SKILL.md) for playback or duels. Character rendering does not require registering `sugar()` on a Sui client.

Use the public Sugar SDK entrypoints. Check the installed package documentation or source before choosing an unfamiliar API; keep private exports and internal helpers out of app code.

## Default workflow

1. Confirm the app already uses the current Mysten Sui client APIs.
2. Install or verify `@sugar/sdk`, `@mysten/sui`, and `@mysten/bcs`, matching the SDK package's Node.js and peer dependency requirements.
3. Extend the existing Sui client with `sugar()`.
4. Keep all Sugar transaction creation and serialization on that extended client instance.
5. Use `client.sugar.getConfig()` for supported coins, object ids, NFT V1 package id, and price info when the UI or diagnostics need resolved config.
6. Route game transaction work to `create-standard-games` or `create-pvp-games` after setup. Route SweetHouse deposits, redeem requests, or delayed self-claims to [sweethouse](../sweethouse/SKILL.md), partner attribution or referral claims to [referrals](../referrals/SKILL.md), and NFT V1 catalog, ownership, or mint flows to [sugar-nft](../sugar-nft/SKILL.md).

## Public surface

Optional extension fields (`config`, `partner`, and `cacheTtl`) accept explicit `undefined`, including with `exactOptionalPropertyTypes`. This behaves like leaving the option out. Any values supplied in nested config overrides must still be valid.

The package exposes these public entrypoints:

- `@sugar/sdk`
- `@sugar/sdk/games`
- `@sugar/sdk/utils`

Use these public imports:

```ts
import { sugar } from '@sugar/sdk';
import type { SugarCoin, SugarNetwork } from '@sugar/sdk';
import { GAMES, type PvPGame, type StandardGame } from '@sugar/sdk/games';
import { parseCoinType, toBigInt, toU8, toU16, toU32 } from '@sugar/sdk/utils';
```

The package root exposes `sugar`, `SugarClient`, `SUGAR_COINS`, `SUPPORTED_SUI_NETWORKS`, `SugarCoin`, and `SugarNetwork`. `SUGAR_COINS` is the readonly list of coin keys (`sui`, `usdc`); `SugarCoin` derives from it. Game ids and game-specific option types live in `@sugar/sdk/games`, alongside `COIN_SIDES` (`heads`, `tails`) and `PVP_COINFLIP_ACTIONS` (`create`, `join`, `cancel`). Reuse these lists in selectors and validation. Parser and numeric helpers live in `@sugar/sdk/utils`.

For a coin selector, read keys and metadata from `client.sugar.getConfig().coins`.

Use these game-specific public types when useful: `GAMES`, `Game`, `StandardGame`, `PvPGame`, `CoinSide`, `PvPCoinflipAction`, `CreateGameBetOptions`, `CoinflipTransactionOptions`, `KenoTransactionOptions`, `LimboTransactionOptions`, `PlinkoTransactionOptions`, `RangeTransactionOptions`, `SoccerTransactionOptions`, `WheelTransactionOptions`, `CreatePvPCoinflipTransactionOptions`, `JoinPvPCoinflipTransactionOptions`, and `CancelPvPCoinflipTransactionOptions`.

Use these utilities instead of local replacements when relevant: `parseCoinType`, `toBigInt`, `toU32`, `toU16`, `toU8`, `DEFAULT_QUERY_LIMIT`, `DEFAULT_GAS_BUDGET_MIST`, `RANGE_POINT_LIMIT`, `DEFAULT_RANGE_SCALE`, and `DEFAULT_LIMBO_MULTIPLIER_SCALE`. For event helpers, Move float/i64 conversion, and raw generated numeric parsing, use [event-parsing](../event-parsing/SKILL.md).

Utility behavior:

- `toBigInt(value)` accepts `bigint`, finite numbers, integer strings, and booleans. Number inputs truncate toward zero (`5.9` becomes `5n`); it does not convert currency decimals into base units. Convert UI amounts with the configured coin decimals before calling builders, and prefer exact strings or `bigint` over imprecise numbers. Invalid inputs throw `TypeError`; negative normalized values throw `RangeError`.
- `toU8(value)`, `toU16(value)`, and `toU32(value)` validate integer inputs in `0..255`, `0..65535`, and `0..4294967295`, respectively; they do not truncate fractions.
- `DEFAULT_QUERY_LIMIT` is `50`. `getPvPCoinflipGames()` uses it when options are omitted; include `limit: DEFAULT_QUERY_LIMIT` when also passing a cursor or another option.
- `parseCoinType(type)` extracts the normalized first generic coin type from a Move object type string.
- Event parsing, `parseGameDetails`, and raw Move float/i64 conversion belong in [event-parsing](../event-parsing/SKILL.md).

Do not import individual runtime game builders from `@sugar/sdk`. Use the registered extension:

```ts
client.sugar.tx.createGameBet({ game, ...options });
client.sugar.tx.pvpCoinflip.createGame(options);
client.sugar.tx.pvpCoinflip.joinGame(options);
client.sugar.tx.pvpCoinflip.cancelGame(options);
client.sugar.tx.referral.claimCommission(options);
client.sugar.tx.referral.claimLevelUpUsdRewards(options);
client.sugar.tx.nftV1.mint(options);
client.sugar.tx.sweetHouse.deposit(options);
client.sugar.tx.sweetHouse.redeemRequest(options);
client.sugar.tx.sweetHouse.claimOwnRedeemRequestAfterDelay(options);
client.sugar.getPvPCoinflipGames(options);
client.sugar.serializeTransactionToBase64({ transaction: tx });
```

## Runtime and installation

Use an ESM project (`"type": "module"`) with Node.js `^24.0.0 || ^26.0.0`. Match the installed SDK's peer dependency ranges for `@mysten/sui@2` and `@mysten/bcs@2`; pre-2.0 Sui clients are unsupported. Install the SDK through npm:

```bash
npm install @sugar/sdk @mysten/sui@2 @mysten/bcs@2
```

Check npm availability and dist-tags before installation. The APIs documented here will be available in the next beta release. Once published, use `npm install @sugar/sdk@beta @mysten/sui@2 @mysten/bcs@2` or a published exact version. The unqualified command above selects `latest` and requires a stable release. A public repository does not establish that a package has been published.

Keep the package version your application already requires. These instructions follow the npm packages in `SugarCasino/ts-sdks`. A skill version does not specify an SDK release.

## Client setup

Extend the Sui gRPC client:

```ts
import { SuiGrpcClient } from '@mysten/sui/grpc';
import { sugar } from '@sugar/sdk';

const client = new SuiGrpcClient({
	baseUrl: 'https://fullnode.testnet.sui.io:443',
	network: 'testnet',
}).$extend(sugar());
```

For partner attribution, use the [referrals](../referrals/SKILL.md) skill.

If the app uses a custom extension name, preserve it consistently:

```ts
const client = new SuiGrpcClient({ baseUrl, network }).$extend(sugar({ name: 'sugarGames' }));

client.sugarGames;
```

If the published defaults lag a deployment, patch config through `sugar({ config })` instead of forking SDK internals:

```ts
const client = new SuiGrpcClient({ baseUrl, network }).$extend(
	sugar({
		config: {
			packageIds: {
				nftV1: '0x...',
				coinflip: '0x...', // Optional MVR-backed package override.
			},
			objectIds: { sweetHouse: '0x...' },
			coins: {
				sui: {
					coinType: '0x2::sui::SUI',
					decimals: 9,
					priceInfoObjectId: '0x...',
				},
			},
		},
	}),
);
```

## Serialization

Serialize the transaction when the wallet, backend, or transport needs unsigned bytes:

```ts
const tx = client.sugar.tx.createGameBet({
	game: 'coinflip',
	owner,
	coinType: '0x2::sui::SUI',
	stake: 1_000_000_000n,
	side: 'heads',
});

const base64 = await client.sugar.serializeTransactionToBase64({
	transaction: tx,
});
```

## Parameters and events

Use `client.sugar.getGameParameters({ game, coinType, ...options })` when an app needs live on-chain game bounds or RTP parameters. `coinType` is required because parameters are stored per game and coin type. The SDK already converts generated Move float fields to JavaScript numbers.

The SDK caches parsed parameters for `cacheTtl`, which defaults to 30 minutes. Pass `ignoreCache: true` to force an on-chain refresh when stale parameters would be risky. Pass a non-positive `cacheTtl` to `sugar({ cacheTtl })` to disable SDK-managed game parameter caching, or call `client.sugar.reset()` to clear cached reads for that extension instance.

For event parsing, raw generated BCS numeric conversion, and direct BCS event helpers, use [event-parsing](../event-parsing/SKILL.md).

## Gotchas

- Invalid package/object ids, coin metadata (`coinType`, `decimals`, or `priceInfoObjectId`), or missing price-info object ids throw `TypeError`. Unsupported networks and coin types throw `RangeError`.
- Keep frontends and backends on the same `owner` wallet address used to build transactions.
- Keep amounts as `bigint` once they leave the UI layer.
- Use `client.sugar.getConfig().coins` for supported `coinType` and `decimals`; do not duplicate decimal constants in app code unless runtime config requires it.
- The resolved `coins` dictionary has a null prototype. Use `Object.hasOwn(coins, key)` for key checks; `Object.keys()`, `Object.values()` and JSON serialization work as usual.
- Prefer SDK-resolved supported coin metadata from `client.sugar.getConfig()` for debugging, inspection, or UI coin selectors; simple examples can pass the expected coin type directly.
- Standard games resolve the price-info object id from the selected coin's `priceInfoObjectId` metadata.
- `packageIds.nftV1` is configured by network because it is not resolved from MVR. Generated game, referral, and core bindings currently use `@suigar/*` MVR names by default, with optional `packageIds` entries for explicit package overrides. These on-chain names are independent of the npm `@sugar/*` scope; verify installed bindings before overriding them. Use `objectIds` for singleton objects such as `sweetHouse` and `nftV1Factory`.
- Use `client.sugar.getConfig().packageIds.nftV1` and `objectIds.nftV1Factory` for NFT V1 catalog, ownership, and mint flows; use [sugar-nft](../sugar-nft/SKILL.md) for that flow.
- Use [sweethouse](../sweethouse/SKILL.md) for public pool deposits, redeem requests, and delayed redeem-request claims. Those builders live under `client.sugar.tx.sweetHouse` and are not standard game bets.
- Use `SugarCoin` and `SugarNetwork` when app code needs supported coin or network types.
- For object reads, parse object `content`, not `objectBcs`.
- Do not hand-decode `BetResultEvent.game_details`; use [event-parsing](../event-parsing/SKILL.md).
- Do not move SDK runtime builders out of `client.sugar.tx` or rely on private package paths.

## Implementation checklist

1. Install and import `@sugar/sdk`, `@mysten/sui`, and `@mysten/bcs`.
2. Extend the current Sui client with `sugar()`.
3. Confirm the client uses the intended supported network.
4. Keep transaction creation and serialization on the same extended client instance.
5. Keep the consuming app on ESM and pass the explicit `network` required by current client constructors.
6. Route game-specific work to the standard or PvP skill after base setup; use [sweethouse](../sweethouse/SKILL.md) for SweetHouse public pool flows, [sugar-nft](../sugar-nft/SKILL.md) for NFT V1 catalog/ownership/mint flows, [event-parsing](../event-parsing/SKILL.md) for event or raw numeric parsing, and [referrals](../referrals/SKILL.md) for attribution or reward claims.
