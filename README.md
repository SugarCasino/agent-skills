# Sugar agent skills

> Release status: verify project ownership of the new npm/JSR namespace and the exact approved release before installing the new package names. This source migration does not publish packages.

Skills for coding agents building with Sugar on Sui and rendering Suifrens characters.

Skills follow the [Agent Skills](https://agentskills.io/) format.

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

Set up `@sugarcasino/sdk` in an application, wire the `sugar()` client extension, resolve config, serialize transactions, and use public SDK exports.

Use when:

- Installing or scaffolding the base Sugar SDK integration
- Configuring `SuiGrpcClient` with `sugar()`
- Working with SDK config, supported coins, or serialization
- Routing event parsing work to the dedicated event-parsing skill

### event-parsing

Parse Sugar SDK events and raw generated numeric values through public SDK utilities and generated BCS helpers.

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

Read the Sugar NFT V1 catalog, list owned NFTs, and build NFT V1 mint transactions using SDK-resolved package and object ids plus BCS helpers.

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

Build PvP game flows with `@sugarcasino/sdk`. PvP Coinflip has its own specification; add a separate specification for each new PvP game.

Use when:

- Creating, joining, or canceling PvP Coinflip matches
- Listing unresolved PvP Coinflip lobby games with the SDK default page size and derived coin types
- Reading PvP Coinflip game objects or events
- Keeping PvP transaction flows separate from standard game builders

### sugar-mcp

Install, configure, operate, or troubleshoot the `@sugarcasino/mcp` server, bundled MCP App, or plugin bundle for Codex, Claude Code, and Cursor.

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

Integrate capy and bullshark rendering, palettes, accessories, static gestures, runner and football kits, standalone SVG exports, and the Fastify SVG/PNG example.

### suifrens-animation

Configure animation playback, custom part sequences, battle sprites and scripted duels. Covers the animation object, iteration counts, play state, replay triggers, and nested duel attack steps.

## Usage

Once installed, agents can use the skills for matching tasks.

Examples:

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

## Development checks

Install dependencies with pnpm:

```bash
pnpm install
```

Run skill evals with Anthropic:

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

Validate the repository without an LLM provider:

```bash
pnpm run validate
```

This checks skill frontmatter, directory/name consistency, line limits, eval structure, and relative Markdown links.

## Skill structure

Each skill contains:

- `SKILL.md` - Instructions for the agent
- `scripts/` - Helper scripts for automation (optional)
- `references/` - Supporting documentation (optional)

## License

MIT
