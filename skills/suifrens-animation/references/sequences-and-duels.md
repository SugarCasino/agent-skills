# Custom sequences and scripted duels

Import `AnimationPresetName` from `/animation` to validate built-in core and battle preset names with `satisfies AnimationPresetName`. The union follows every registered preset catalogue; lookup APIs still accept dynamic strings and return no sequence for unknown names. Renderers play core presets immediately and load each deferred catalogue on first use. Requests share that catalogue load, and a failed load can be retried independently. Synchronous `/animation` helpers use the full catalogue.

## Custom part animation

```ts
import type { AnimationSequence } from '@sugarcasino/suifrens-display-system/animation';

const nod = {
	name: 'nod',
	duration: 600,
	tracks: [
		{
			part: 'head',
			keyframes: [
				{ at: 0, pose: { rotate: 0 } },
				{ at: 300, pose: { rotate: 8 }, ease: 'easeInOut' },
				{ at: 600, pose: { rotate: 0 } },
			],
		},
	],
} satisfies AnimationSequence;
// Use animation={{ sequence: nod, playback: { iterations: 1 } }}.
```

Sequence duration and keyframe `at` values use milliseconds. Parts are `body`, `head`, `leftArm`, `rightArm`, `leftLeg`, `rightLeg`, `tail`, and `fin`. Check the species' registered parts before relying on a tail or fin. Pose values support rotation, translation and scale. Keep animation in the library's part system so attached accessories follow the appropriate body parts.

## Ground shadows

Enable the boolean `shadow` prop on the image, for example `<SuiFrenImage attributes={attributes} shadow animation={{ preset: 'jump' }} />`. A built-in preset supplies its own shadow behavior. For a custom sequence, check that the installed package exposes `AnimationSequence.shadow` in `/animation`, then set the field on the sequence. Its public type is `AnimationShadow`.

| Sequence value | Behavior with the image shadow enabled | Examples |
| --- | --- | --- |
| `'static'` (default) | Keeps the standing footprint at its usual size and position. | Custom sequences that omit the field |
| `'falling'` | Projects the moving body onto the ground as it rotates into the landing pose. | `death`, `death2`, `death3` |
| `'airborne'` | Shrinks and fades the footprint as the body rises. Animate negative `translate.y` on the `body` track and return to zero on landing. | `jump`, `walkJumpSit` |

## Duels

Import `useDuelScript`, `SuiFrenBattleSprite`, and `MinecraftHealthBar` from `/battle`. Import `DuelScript`, `DuelAttackStep`, and component/hook types from `/battle`.

```ts
import type { DuelScript } from '@sugarcasino/suifrens-display-system/battle';

const script = {
	name: 'Demo duel',
	steps: [
		{
			type: 'attack',
			label: 'Left punches',
			attacker: { side: 'left', preset: 'punch', expression: 'angry' },
			target: { damage: 2, preset: 'hit', delayMs: 140 },
			afterMs: 140,
		},
		{ type: 'victory', label: 'Left wins', side: 'left', iterations: 3 },
	],
} satisfies DuelScript;
```

The attack target is always the opposite fighter. Do not supply an explicit target side. Attacker options belong in `attacker`; impact options belong in `target`. Victory uses `iterations` (default five).

Call `useDuelScript({ script, baseExpression: { left: 'happy', right: 'happy' }, ...options })` in a React component. It returns `{ state, restart, stop, reset }`. Stabilize scripts and option objects when appropriate so a render does not unintentionally restart an effect.

Use `state.left` and `state.right` as the source of each fighter's health, maximum health, expression and animation. Pass those to the battle sprite with the matching attributes. Use that same health state for the health bar. The hook controls the scene's animation. For scenes that show an on-chain game result, build the script from the verified result; the animation cannot determine the outcome.

`SuiFrenBattleSprite` owns its embedded renderer, facing transform and health bar. Its API does not automatically accept every `SuiFrenImage` prop. Check its declared props before adding accessories or gestures to a battle sprite. Use `hitFlashTrigger` for repeated hit flashes without remounting the animated fighter.
