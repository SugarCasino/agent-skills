---
name: suifrens-display
description: Integrate @sugar/suifrens-display-system character rendering in React, standalone SVG exports, or its Fastify SVG/PNG server. Use for capy and bullshark attributes, external versus inline SVG accessories, palettes, skins, expressions, static gestures, runner or football kits. Use suifrens-animation for playback and duel scripts; Sugar NFT V1 reads and minting belong to sugar-nft.
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

Render the native SVG characters through `@sugar/suifrens-display-system`. Keep character artwork and accessory geometry in the library. Fix library issues in its source rather than redrawing characters or patching installed output.

## Rendering workflow

Use the public exports and types of `@sugar/suifrens-display-system`. Read [references/rendering.md](references/rendering.md) for attributes, renderer selection, accessories, kits and gestures.

For the Fastify example, HTTP image URLs, network selection, PNG dimensions, logos or shadows, read [references/http-rendering.md](references/http-rendering.md).

For motion, playback or battles, use [suifrens-animation](../suifrens-animation/SKILL.md).

## Working boundaries

This package handles character appearance. Wallet connections, NFT ownership reads, and minting are outside its scope; [sugar-nft](../sugar-nft/SKILL.md) covers Sugar NFT V1. Use the Suifrens schema for character attributes.

For a library defect, work in `SugarCasino/ts-sdks`, under `packages/suifrens-display-system`, following its contribution instructions. Do not edit `node_modules`, compiled `dist`, or introduce dependency patches. A consumer integration request does not authorize publishing a library release.

## Verification

Run the consumer's typecheck and build for changed imports and props. Inspect the relevant species at the intended size, including head/full rendering and accessories. For SVG exports, confirm that the output can resolve every referenced image.
