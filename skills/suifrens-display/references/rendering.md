# Rendering API

## Imports and attributes

```tsx
import { SuiFrenImage } from '@sugarcasino/suifrens-display-system';
import type { CapyAttributes } from '@sugarcasino/suifrens-display-system/attributes';
import { SuiFrenImage as EmbeddedSuiFrenImage } from '@sugarcasino/suifrens-display-system/embed';

const attributes = {
	mainColor: 'a17e52',
	secondaryColor: 'd5cbb1',
	skin: 'basic',
	expression: 'happy',
	earShape: 'default',
} satisfies CapyAttributes;

export function Portrait() {
	return <SuiFrenImage attributes={attributes} detail="head" renderSize={128} />;
}

export function ExportableCharacter() {
	return <EmbeddedSuiFrenImage attributes={attributes} renderSize={512} />;
}
```

Use the root `SuiFrenImage` for both species. Import types from their feature entries. Do not import private `src` or `dist` files.

`SuiFrenAttributes` is a union distinguished by `earShape` for capys and `finStyle` for bullsharks. Do not add a synthetic `species` property to the attributes. `SuiFrenSpecies` is the separate `'capy' | 'bullshark'` union used by APIs that request a species.

Use palette keys from `COLORS` in `/colors`, without a leading `#`, for `mainColor` and `secondaryColor`. Write hexadecimal color literals in lowercase, including values without `#`. Use `SUIFREN_SKINS` and `EAR_GENE_TO_EAR_SHAPE` from `/attributes`, and `ALL_EXPRESSIONS`, `POSITIVE_EXPRESSIONS`, or `NEGATIVE_EXPRESSIONS` from `/expressions`. Both species share `SuiFrenSkin` and `SuiFrenExpression` types. Import `SuiFrenAppearance` from `/attributes` for their shared color, skin and expression fields. Preserve the original spelling of catalogue values.

Both React image renderers hide the ground shadow by default. Pass the boolean `shadow` prop to show it. For an animated image, the selected sequence controls how the visible shadow moves. The Fastify HTTP example enables shadows by default.

## External assets and SVG exports

The root renderer uses cacheable external accessory SVGs. The `/embed` entry embeds catalogue artwork and is the choice for standalone SVG markup. Server-side static markup does not run animation effects.

An explicit accessory `renderOptions.assetSrc` overrides ordinary catalogue artwork in both renderers. The embedded renderer still references an explicit override directly: a remote URL is not made self-contained. Supply a self-contained SVG data URL if an export must work offline. Check asset URLs in the consumer's built app too; a sibling source alias can hide broken URLs.

## Accessories, kits and gestures

Pass catalogue entries or custom `SuiFrenAccessory` objects from `/accessories` with `name`, `type`, `category` and `renderOptions`. Use optional access for missing slots returned by `getAccessoriesByType`.

Import `ACCESSORIES`, `VIP_CROWN_ACCESSORIES` and crown helpers from `/accessories`; import `buildRunnerAccessories`, `resolveRunnerKit`, `SOCCER_TEAM_KITS`, `resolveSoccerCountryCode`, and `resolveSoccerTeamKit` from `/kits`.

```tsx
import { buildRunnerAccessories } from '@sugarcasino/suifrens-display-system/kits';

const outfit = buildRunnerAccessories({ color: '#ec7da6', number: 3, mirrorBib: true });
// Pass outfit as the image component's accessories array.
```

Runner colors use six-digit hex values and bib numbers use integers 1–99; invalid values use library defaults. Set `mirrorBib` when the consumer mirrors the whole character. The helper supports both species and preserves separate leg layers.

For custom metadata, outfit options belong in `renderOptions.kit`: `{ type: 'runner', color: '#ec7da6', number: 3 }` or `{ type: 'soccer', country: 'FR', side: 'away' }`. A soccer kit may use a custom `team` instead of `country`; do not set both. Read the selected `SoccerTeamKit` type for custom team metadata instead of inventing fields. Keep football game country IDs separate from visual kit country codes.

The shared static `gesture` prop accepts `'thumbs_up'`, `'thumbs_down'`, and `'point'`. Gestures work on both full-body species and both image renderers. A held object takes precedence, and head portraits ignore gestures. Static gestures do not require an animation timeline.
