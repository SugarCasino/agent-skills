# Sugar agent skills

These skills help coding agents build Sugar apps on Sui and render Suifrens characters. They follow the [Agent Skills](https://agentskills.io/) format.

[![skills.sh](https://skills.sh/b/SugarCasino/agent-skills)](https://skills.sh/SugarCasino/agent-skills)

## Installation

Browse available skills:

```bash
npx skills list SugarCasino/agent-skills --list
```

Install a specific skill:

```bash
npx skills add SugarCasino/agent-skills --skill sugar-mcp
```

Install all skills:

```bash
npx skills add SugarCasino/agent-skills
```

## Available skills

### installation

Install `@sugarcasino/sdk` and connect it to a Sui client with `sugar()`. Covers configuration, transaction serialization, and public exports.

Use when:

- Installing or scaffolding the base Sugar SDK integration
- Configuring `SuiGrpcClient` with `sugar()`
- Working with SDK config, supported coins, or serialization
- Routing event parsing work to the dedicated event-parsing skill

### event-parsing

Decode Sugar events with the SDK parsers and generated BCS helpers, and convert raw Move numeric values.

Use when:

- Decoding `BetResultEvent.game_details`
- Using `parseSugarEvent`, `parseGameEvent`, or `parseGameDetails`
- Parsing PvP Coinflip or SweetHouse generated BCS event payloads
- Safely converting raw Move float or i64 values
- Handling core API event bytes, vector game details, and referral claim events

### referrals

Configure partner attribution and claim referral rewards through `@sugarcasino/sdk`.

Use when:

- Configuring `sugar({ partner })` with a partner wallet address
- Checking claimable referral commission or level-up USD rewards
- Building unsigned referral claim transactions
- Correcting referral values incorrectly placed in game metadata

### sugar-nft

Read the Sugar NFT V1 catalog, list owned NFTs, and build mint transactions. Package and object ids come from the SDK config; generated BCS helpers decode the objects.

Use when:

- Listing an owner's NFT V1 objects
- Reading and decoding the NFT V1 catalog factory
- Building `client.sugar.tx.nftV1.mint` from a catalog `specId`
- Deriving the NFT type with the active SDK BCS helper
- Keeping NFT reads network-aware without hard-coded package ids

### sweethouse

Build SweetHouse public pool transactions with `@sugarcasino/sdk`.

Use when:

- Depositing supported coins into the SweetHouse public pool
- Creating redeem requests from SweetHouse staked coins
- Claiming your own redeem request after the on-chain delay
- Decoding SweetHouse redeem request events
- Keeping SweetHouse liquidity flows separate from game, referral, NFT, or MCP APIs

### create-standard-games

Build standard single-player Sugar game transactions with `@sugarcasino/sdk`.

Use when:

- Building `coinflip`, `keno`, `limbo`, `plinko`, `range`, `soccer`, or `wheel` bet transactions
- Mapping UI inputs to `client.sugar.tx.createGameBet({ game, ...options })`
- Reviewing standard game amount, metadata, or coin handling
- Fixing AI-generated standard Sugar game code

### create-pvp-games

Create PvP game flows with `@sugarcasino/sdk`, starting with PvP Coinflip. Each supported game has its own specification.

Use when:

- Creating, joining, or canceling PvP Coinflip matches
- Listing unresolved PvP Coinflip lobby games with the SDK default page size and derived coin types
- Reading PvP Coinflip game objects or events
- Keeping PvP transaction flows separate from standard game builders

### sugar-mcp

Set up and use the `@sugarcasino/mcp` server and bundled MCP App, with plugin installation for Codex, Claude Code, and Cursor.

Use when:

- Adding the Sugar MCP server to an MCP client
- Installing the Sugar plugin from a supported marketplace
- Reading Sugar config or game metadata through MCP tools
- Pairing browser wallets, checking balances, or listing coin objects
- Setting up, inspecting, or funding a local session wallet
- Browsing the NFT V1 catalog and a wallet's matching NFTs through `list_nfts`
- Building NFT V1 mint transactions through `build_nft_v1_mint_transaction`
- Building SweetHouse deposit, redeem request, or delayed redeem claim transactions
- Building, dry-running, or explicitly executing transactions in read-only, build, dry-run, or execute mode
- Explaining MCP support boundaries for Sugar games

### suifrens-display

Render capys and bullsharks with palettes, accessories, gestures, and runner or football kits. Includes standalone SVG exports and the Fastify SVG/PNG example.

### suifrens-animation

Animate Suifrens with custom part sequences, battle sprites, and scripted duels. Covers playback settings, iteration counts, replay triggers, and nested attack steps.

## Usage

After installation, ask your agent to work on a task covered by a skill. For example:

```text
Set up Sugar in this app
```

```text
Build a coinflip transaction
```

```text
Deposit into the SweetHouse public pool
```

```text
Configure the Sugar MCP server
```

```text
Install the local Sugar plugin in Codex
```

```text
Add a PvP Coinflip lobby flow
```

```text
List this wallet's Sugar NFT V1 objects
```

```text
Mint a Sugar NFT V1 from this catalog specId
```

```text
Use the Sugar MCP to read the testnet config and list supported games
```

```text
Build a read-only Sugar MCP transaction plan for a 1 SUI coinflip on heads
```

```text
Dry-run a Sugar MCP limbo transaction for this wallet with a 2.5x target multiplier
```

```text
Use Sugar MCP to build a read-only SweetHouse redeem request plan
```

```text
Use Sugar MCP to build an unsigned PvP Coinflip create transaction for 1 SUI on tails
```

```text
Use Sugar MCP to set up a local session wallet and show its balances
```

```text
Render a Suifren with a runner kit and a thumbs-up gesture
```

```text
Add Suifrens animation playback and a scripted duel
```

## Package releases and compatibility

The public npm packages are `@sugarcasino/sdk`, `@sugarcasino/mcp`, and `@sugarcasino/suifrens-display-system`. This repository distributes skills through the skills CLI; its private package manifest is development tooling and is not published to npm.

The SDK workspace has been merged into [Sugar TypeScript SDKs](https://github.com/SugarCasino/ts-sdks). The APIs documented by these skills will be available in the next beta release. Before installing, check the package's published versions and dist-tags. Use a published exact version or `@beta` for a beta release; use `@latest` only once a stable release is available. Keep an application's existing version pin unless an upgrade is requested.

The npm packages bundle skill entrypoints that fetch full instructions and relative references from `agent-skills/main`. Merge matching skill updates before publishing the SDK packages. Skill metadata versions are independent of npm package versions. See [public release coordination](https://github.com/SugarCasino/.github/blob/main/docs/publication.md).

The skills follow the public SDK APIs on `main` for the next beta release. Use `client.sugar.getConfig().coins` for supported coin keys and metadata, and pass `SuiClientTypes.Event` values to the event parsers.

## Development checks

Install dependencies with pnpm:

```bash
pnpm install
```

GitHub Actions is restricted to the exact action revisions used by the workflows. When updating an action pin, update the repository action allowlist as well.

Pull requests run validation, lint, and tests without an API key. Anthropic evaluations run manually or on the monthly schedule when the repository has an API key. Results are attached to the workflow run.

To run skill evals locally with Anthropic:

```bash
ANTHROPIC_API_KEY=... pnpm run eval
```

Run evals for one skill:

```bash
ANTHROPIC_API_KEY=... pnpm run eval -- --skill installation
```

Check linting and formatting with Oxlint and Oxfmt:

```bash
pnpm run lint
```

Check skill structure and links locally:

```bash
pnpm run validate
```

The validator checks frontmatter, matching folder and skill names, line limits, eval structure, and relative Markdown links. It does not call an LLM.

Run `pnpm test` to check the publication identity rules. The [contribution policy](docs/git-publication-privacy.md) explains how these apply to SugarCasino members and external contributors.

The skills start at version `1.0.0`. SDK and MCP packages have their own versions, so keep any version your app already requires. Package names and install commands follow [Sugar TypeScript SDKs](https://github.com/SugarCasino/ts-sdks).

## Skill structure

Each skill contains:

- `SKILL.md` - Instructions for the agent
- `scripts/` - Helper scripts for automation (optional)
- `references/` - Supporting documentation (optional)

## License

MIT
