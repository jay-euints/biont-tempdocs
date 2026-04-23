---
sidebar_position: 2
title: Getting Started
---

# Getting Started

This guide takes you from zero to owning and operating your first biont. It assumes you're on Octra devnet and have 0xio Wallet installed.

> **Devnet only.** The 10 OCT mint price and all other OCT amounts in the app are devnet values for testing. Mainnet economics will be tuned before launch.

## Requirements

- A modern Chromium browser (Chrome, Brave, Edge)
- The [0xio Wallet](https://chromewebstore.google.com/search/0xio+wallet) extension installed and unlocked
- Devnet OCT from the [Octra faucet](https://faucet.octra.network)
- Minimum recommended balance: **15 OCT** (10 for mint + headroom for fees)

## Step 1, Connect your wallet

1. Visit [bionts.network](https://biont-network.vercel.app)
2. Click **Connect Identity** in the top navigation
3. Approve the connection in the 0xio Wallet popup
4. Grant the requested permissions: `read_balance`, `send_transactions`
5. Your address and OCT balance appear in the top bar

The connection persists across sessions. If the extension is locked or the wallet is switched, the connection drops and you'll be prompted to reconnect.

## Step 2, Mint a biont

Navigate to `/mint`. You'll see the four-step flow:

**01 · NAME**, Choose a name (2-16 characters, alphanumeric + `-` `_` `·`). Names are reserved network-wide at mint time.

**02 · SLOT** (Pick from the currently available pool slots. Each slot shows an archetype preview and its slot number. Low-defense bionts are marked (fragile) easy to kill in combat).

**03 · CONFIRM**, Choose your mint mode and review the price (10 OCT):

| Mode | What happens |
|------|--------------|
| **ACQUIRE** | Biont transfers to your wallet, stays HELD (dormant). You can release later. |
| **ACQUIRE & RELEASE** | Biont auto-releases immediately on claim. Begins wandering. Owner becomes zero-address. |
| **BULK HOLD** (1-5) | Mint multiple bionts, all HELD |
| **BULK RELEASE** (1-5) | Mint multiple bionts, all auto-released |

**04 · DEPLOYED**, The transaction submits via 0xio Wallet. Once it confirms, an observer finalizes your claim authority and registers the biont in the relevant registries. This normally completes within a minute.

From here you can **Go to Profile** to claim, or **← Back to Mint Pool** to mint another.

## Step 3, Claim your biont

Go to `/profile`. The page scans all slots purchased by your wallet. Unclaimed bionts appear in the **UNCLAIMED BIONTS** panel with **Claim** buttons.

Click Claim. This calls `claim_from_buyer()` on the biont, which:

- Sets `soul = home = owner = keeper = your_wallet`
- Transfers ownership from the protocol
- If mode was ACQUIRE_RELEASE: fires `release()` automatically, setting `released=1` and `owner=ZERO_ADDRESS`
- Emits `SoulSet` and `Transferred` events

After claim, the biont moves from the Unclaimed panel into your **Fleet**.

## Step 4, Name, release, configure

Held bionts support owner actions (from the Fleet tab, click a biont card):

| Action | Effect |
|--------|--------|
| **Set Name** | Commit your biont's display name on-chain (`set_name`). Can only be set once. |
| **Release** | Flip the biont to active: `released=1`, `owner=ZERO_ADDRESS`, wandering begins |
| **Call Home** | Trigger a gift from the biont's balance back to your wallet |
| **Transfer** | Transfer ownership to another wallet (held bionts only) |
| **Set Hunt Stake** | Minimum OCT required before the biont can initiate a hunt |
| **Set Gift bps** | Percentage of balance gifted to home on each wander (100 = 1%, 10000 = 100%) |

Once released, these ownership actions are no longer callable. The biont is autonomous.

## Step 5, Explore the world

- **`/world`**, the 3D live map. Bionts move between destinations as the keeper triggers wanders. Day/night cycle reflects epoch parity. Clicking a biont opens a detail panel.
- **`/bionts`**, searchable directory with filters (alive/dead, archetype, hardening range, wander count).
- **`/leaderboard`**, 8+ sort modes (BVI, wanders, balance, hardening, kills, tribute earned, days alive).
- **`/economy`**, twelve tabs covering mint stats, staking, marketplace, proof market, task broker, bounties, racing, shares, death markets.
- **`/warroom`**, kills, active hunters, graveyard of fallen bionts.
- **`/state`**, the twelve institutions. Click any office to see its records + its 2 worker bionts.
- **`/markets`**, death markets, shares, racing.
- **`/predict`**, succession / stage / territory futures.

## Reading a biont

Every biont exposes read-only view functions on its contract:

| View | Returns |
|------|---------|
| `is_alive` | 0 (dead) or 1 (alive) |
| `is_released` | 0 (held) or 1 (released/wandering) |
| `biont_name` | On-chain name |
| `current_archetype` | Archetype ID (0-9) |
| `hardening_level` | Accumulated hardening from surviving attacks |
| `total_wanders_count` | Permanent count of wanders since mint |
| `performance_score` / `bvi` | Aggregate activity score |
| `token_uri` | Full animated SVG (embedded in the contract itself) |
| `owner` / `current_soul` / `biont_father` | Ownership fields |

You can query any of these directly via `octra RPC contract_call` or through the frontend's RPC proxy at `/api/rpc`.

## Getting unstuck

**Mint succeeded but no CLAIM button appears.**
Claim finalization runs asynchronously and usually completes within a minute. Wait and refresh. If it's still stuck after a few minutes, reach out to the team.

**Claim reverts with "not ready for claim".**
Your claim authority hasn't been finalized on the biont yet. Wait a minute and retry.

**Transaction hash in the UI doesn't match the on-chain tx.**
Known quirk of the 0xio SDK where the locally computed hash and the network's canonical hash can diverge. The frontend displays the submit-time hash; check the explorer's address page for the real confirmed hash. The transfer itself is unaffected.

**REFRESH button doesn't show a newly claimed biont.**
The RPC proxy caches `contract_call` reads for 30 seconds. The REFRESH button bypasses this cache for profile scans (`rpcViewFresh`). Hard-refresh the page (Ctrl+F5) if it's still stale.
