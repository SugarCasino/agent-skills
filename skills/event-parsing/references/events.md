# Event and numeric parsing specification

Use this specification when the task is about parsing Sugar events, decoding `BetResultEvent.game_details`, or safely converting raw generated Move numeric structs.

## Public imports

```ts
import {
	fromMoveFloat,
	fromMoveI64,
	isMoveFloat,
	isMoveI64,
	parseGameDetails,
	parseGameEvent,
	parseSugarEvent,
} from '@sugar/sdk/utils';
```

## Game events

Use events from the Sui core API (`SuiClientTypes.Event`), with `eventType`, `module`, and `bcs: Uint8Array`. The parser signatures accept `SuiClientTypes.Event`. At runtime, `parseGameEvent` reads only `eventType` and `module`, while `parseSugarEvent` also needs BCS bytes to decode a payload. Pass the full typed event in TypeScript; do not cast a minimal envelope to bypass the signature.

Prefer `parseSugarEvent(event)` when the raw Sui event includes BCS bytes. It resolves the supported Sugar `game`, decodes the event payload, and includes decoded `gameDetails` for `BetResultEvent`.

```ts
const decoded = parseSugarEvent(event);
if (decoded && 'gameDetails' in decoded) {
	const result = {
		game: decoded.game,
		event: decoded.event.type,
		details: decoded.gameDetails,
	};
}
```

The normalized result is `{ game, event: { type, data } }`, with `gameDetails` only for `BetResultEvent`. Read the raw decoded fields from `decoded.event.data`; the top-level game details parser does not normalize Move float structs inside that data. Unsupported events or missing/non-`Uint8Array` BCS return `null`; malformed supported payloads can throw, so isolate failures per event when processing a mixed stream.

Use manual staged decoding when the app needs direct access to `BetResultEvent` data:

```ts
const parsed = parseGameEvent(event);
if (parsed?.event === 'BetResultEvent') {
	const decoded = client.sugar.bcs.BetResultEvent.parse(event.bcs);
	const details = parseGameDetails({
		game: parsed.game,
		gameDetails: decoded.game_details,
	});
	const price = fromMoveFloat(decoded.adjusted_oracle_usd_coin_price);
}
```

`parseGameEvent(event)` returns `{ game, event }`. Do not use the older `gameId` or `eventName` field names. `parseGameDetails({ game, gameDetails })` decodes known detail keys and preserves on-chain key names.

Generic `GameDetails<Game>` fields may be unavailable to TypeScript when the mapped type does not distribute over the game union. Narrow the outer event with `'gameDetails' in decoded`, confirm the game, and call `parseGameDetails` with a literal identifier for game-specific fields:

```ts
const decoded = parseSugarEvent(event);
if (decoded && 'gameDetails' in decoded && decoded.game === 'keno') {
	const details = parseGameDetails({
		game: 'keno',
		gameDetails: decoded.event.data.game_details,
	});
	const payout = details.payout_amount;
}
```

Checking only `decoded.event.type` does not narrow the outer event union enough to access `decoded.gameDetails`.

Decoded details have a null prototype. Use `Object.hasOwn(details, key)` to check keys, including on `gameDetails` returned by `parseSugarEvent`. Unknown keys such as `__proto__` and `constructor` decode as strings. Use `Object.entries()` or JSON serialization as usual; do not call inherited methods such as `details.hasOwnProperty()`.

For PvP Coinflip, use `client.sugar.bcs.PvPCoinflipGameCreatedEvent`, `PvPCoinflipGameResolvedEvent`, and `PvPCoinflipGameCancelledEvent` when direct event parsing is needed.

For SweetHouse redeem events, use `client.sugar.bcs.RedeemRequestCreatedEvent`; for referral claim events, use `client.sugar.bcs.ReferrerClaimCommissionBalanceEvent` or `client.sugar.bcs.ReferrerClaimLevelUpUsdRewardsEvent`. `parseSugarEvent` is for supported game events. Match the event type before choosing a generated helper.

## Raw Move numerics

`client.sugar.getGameParameters()` already converts generated Move float fields to JavaScript numbers. Use the guards only when raw generated BCS data comes from another path.

```ts
if (isMoveFloat(value)) {
	const number = fromMoveFloat(value);
}

if (isMoveI64(value)) {
	const number = fromMoveI64(value);
}
```

Known vector detail fields are decoded as arrays: for example, Keno `picks` and `drawn_numbers` are `number[]`, while `payout_amount` is `bigint` and `multiplier` is a JavaScript number. Unknown detail keys fall back to string decoding; they are not automatically interpreted as arbitrary numeric fields. Event `metadata` stays a raw vec-map of bytes and follows the application's own encoding contract.

Parsed `u64` and `u128` game detail values return `bigint` to preserve precision. Convert them to strings or decimal values when displaying them.

## Minimal envelopes and union types

When supported by the installed SDK signatures, `parseGameEvent` needs only `eventType` and `module`, and `parseSugarEvent` additionally needs BCS bytes. Guard game-specific union fields with a property check such as `'payout_amount' in decoded.gameDetails`, or pass a literal game to `parseGameDetails` for a precise result.
