---
name: referrals
description: Configure Sugar SDK partner attribution and referral rewards in a Sui application. Use when registering a partner wallet with sugar(), checking referral commission or level-up USD reward balances, building unsigned referral claim transactions, or correcting code that places referral data in game metadata. Use sugar-mcp instead for MCP referral tools.
license: MIT
metadata:
  author: sugar
  version: '1.0.0'
  short-description: Configure Sugar referrals
  tags:
    - sugar
    - sui
    - sdk
    - referrals
---

# Sugar Referrals

Use this skill for application code that imports `@sugarcasino/sdk`. For referral reads or unsigned claims through `@sugarcasino/mcp`, use `sugar-mcp` instead.

Use the public SDK extension for referral calls. It handles the Move calls and applies partner attribution without duplicating it in each game's metadata.

## Partner attribution

Configure attribution once when registering the client extension:

```ts
import { SuiGrpcClient } from '@mysten/sui/grpc';
import { sugar } from '@sugarcasino/sdk';

const client = new SuiGrpcClient({ baseUrl, network }).$extend(
	sugar({ partner: '0xpartner_wallet_address' }),
);
```

`partner` must be a wallet address. Do not pass a campaign slug, label, or display name. Do not set `metadata.partner` or `metadata.referrer` on game transactions; the extension applies the configured partner across supported bet flows.

The SDK normalizes `partner` and throws `TypeError` for invalid addresses, blank strings, or non-string runtime values. `undefined` leaves attribution unset.

Keep the same partner for the lifetime of a client. If attribution changes by campaign or session, select or create a client with the new partner before building transactions.

## Referral rewards

Read the claimable amount before displaying a reward or asking the wallet to claim it. Commission claims use a selected supported coin; level-up USD reward claims use the configured USDC coin.

```ts
const claimableCommission = await client.sugar.view.referral.getCommission({
	owner,
	coinType: '0x2::sui::SUI',
});

const commissionTx = client.sugar.tx.referral.claimCommission({
	owner,
	coinType: '0x2::sui::SUI',
});

const claimableLevelUpRewards = await client.sugar.view.referral.getLevelUpUsdRewards({ owner });
const levelUpRewardsTx = client.sugar.tx.referral.claimLevelUpUsdRewards({
	owner,
});
```

The view methods simulate their matching claim transaction and return `0n` when a claim is unavailable or cannot be simulated. The builders produce unsigned transactions that transfer the claimed coin to `owner`; submit or serialize them through the application's normal wallet flow.

Use `client.sugar.getConfig().coins` for supported commission `coinType` values and display decimals. Do not infer that a balance is claimable simply because a referrer or partner exists.

## Implementation checklist

1. Register `sugar({ partner })` once when attribution is required.
2. Pass a wallet address as `partner`; never put referral values in transaction metadata.
3. Read a claimable amount with the matching `client.sugar.view.referral` method.
4. Build the matching unsigned `client.sugar.tx.referral` transaction only when a claim is requested.
5. Set `owner` to the wallet that will receive and submit the claim.
