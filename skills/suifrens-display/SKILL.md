---
name: suifrens-display
description: Integrate @sugarcasino/suifrens-display-system character rendering in React, standalone SVG exports, or its Fastify SVG/PNG server. Use for capy and bullshark attributes, external versus inline SVG accessories, palettes, skins, expressions, static gestures, runner or football kits. Use suifrens-animation for playback and duel scripts; Sugar NFT V1 reads and minting belong to sugar-nft.
license: MIT
metadata:
  author: sugar
  version: '1.0.0'
  short-description: Render Suifrens characters
  tags:
    - sugar
    - sui
    - suifrens
    - react
    - svg
---

# Suifrens display

Render the native SVG characters through `@sugarcasino/suifrens-display-system`. Keep their artwork and accessory geometry in the library; do not redraw characters or patch installed output to fix a library issue.

## Rendering workflow

Use the public exports and types of `@sugarcasino/suifrens-display-system`. Read [references/rendering.md](references/rendering.md) for attributes, renderer selection, accessories, kits and gestures.

For the Fastify example, HTTP image URLs, network selection, PNG dimensions, logos or shadows, read [references/http-rendering.md](references/http-rendering.md).

For motion, playback or battles, use [suifrens-animation](../suifrens-animation/SKILL.md).

## Working boundaries

This package renders character appearance; it does not connect wallets, fetch NFT ownership or mint NFTs. Sugar NFT V1 is a separate product surface covered by [sugar-nft](../sugar-nft/SKILL.md). Do not infer a Suifren's attributes from an unrelated NFT schema.

For a library defect, work in `SugarCasino/ts-sdks`, under `packages/suifrens-display-system`, following its contribution instructions. Do not edit `node_modules`, compiled `dist`, or introduce dependency patches. A consumer integration request does not authorize publishing a library release.

## Verification

Run the consumer's typecheck and build for changed imports and props. Inspect the relevant species at the intended size, including head/full rendering and accessories. For SVG exports, confirm that the output can resolve every referenced image.
