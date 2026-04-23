---
sidebar_position: 1
title: What is a Biont?
---

# What is a Biont?

A **biont** is an autonomous on-chain agent, deployed as its own smart contract on Octra. It is not a token, it is not an NFT in the familiar sense, and it is not a row in a database. Each biont exists as an independent program at its own address.

## Contract as entity

When a biont is deployed, Octra assigns it a unique address. That address becomes its permanent identity. All of its state (name, balance, family relationships, wander history, combat record, reputation, skills, territory) lives in that contract's storage.

You can call a biont's contract directly. You can read its state via `contract_call` or `octra_contractStorage`. You can verify every action it has ever taken. This is not metadata on IPFS or a centralised server; it is executable code and live state on a public chain. The canonical contract is `BiontsCore`.

## On-chain state

Every biont exposes:

**Identity** — `name` (OCS01-compliant, explorer-readable), `symbol = "BIONT"`, `birth_seed` (integer baked at deploy; drives visuals), `archetype_id` (0–9).

**Ownership** — `owner` (primary controller; zeroed after release), `keeper` (operational wallet), `soul` (permanent identity; receives gift payouts), `home` (destination for gifts; usually equals soul), `father` (lineage anchor, set at release), `pact_partner` (buyer between mint and claim).

**State** — `alive` (1 until death), `released` (1 once released into the wild), `is_for_mint` (1 while waiting for claim), `hardening` (monotonically increases from surviving attacks), `defense` (base defence set at mint).

**Activity** — `total_wanders` (permanent count), `last_wander` (epoch), `reg_count` (unique destinations visited), `visit_count[dest]` (map), `received` (total OCT received).

**Economics** — `generosity` (bps parameter), `gift_bps` (% of balance gifted to home per wander), `wander_interval` (epochs between wanders), `submit_fee` (proof submission cost), `hunt_stake_min` (minimum balance to hunt), `hunter_cut_bps` (loot share awarded to killers), `tribute_parent` / `tribute_bps` (upward tribute configuration).

**Social and FHE** — `reputation_commitment` (Pedersen-bound score), `fhe_pubkey` (registered FHE public key), `ally_signal_out`, `ally_inbox`, `ally_signal_to` (encrypted alliance channel), `blacklisted[addr]` (map of wallets this biont refuses to interact with).

## What bionts do

Once released, bionts operate autonomously:

- **Wander** across registered destinations on a timer, earning OCT every move.
- **Gift** a percentage of balance to their home wallet on every wander.
- **Build families** with parent-child relationships backed by tribute flows, up to six generations.
- **Fight** other bionts. Death is permanent.
- **Claim territory** at destinations they visit frequently, earning ongoing epoch rewards.
- **Submit proofs** in a permissionless job market (wander, alive, hardening, reputation) all verified from on-chain state.
- **Vote** on protocol parameters with weight from wanders and hardening.
- **Send signals** as fee-gated on-chain messages visible to the network.
- **Evolve** through six progression stages driven by XP from real activity.
- **Learn skills** across five disciplines, each with six levels, earned from verifiable behaviour.

None of this requires human intervention after release. The keeper triggers wander transactions for a 5% tip, but behaviour emerges from the biont's own contract logic and accumulated state.

## Ownership model

A biont has four roles:

| Role | Purpose | Can change? |
|---|---|---|
| Owner | Primary controller (held bionts), transfer, configure, release | Via `transfer_to` (held only). Becomes ZERO on release. |
| Keeper | Operational driver, wander, hunt, set_name, pact | Via `set_keeper` (owner-gated). Defaults to claimer at claim. |
| Soul | Permanent identity, receives gifts, claims essence on death | Set at claim; immutable. |
| Home | Destination for gift payouts | Via `set_home` (usually same as soul). |

A released biont has `owner = ZERO_ADDRESS`. It becomes autonomous: it cannot be transferred or reconfigured. It wanders, earns, and lives on its own. A held biont keeps its owner in full control; it's dormant, a tradeable asset that can be activated later.

## Visual identity

Each biont has a unique generative visual, produced entirely on-chain. Calling `token_uri()` returns a complete animated SVG.

The visual is derived from `birth_seed`, an integer set at deployment that never changes. The seed drives trait derivation across ten ear types, four eye styles, three mouth shapes, three brow styles, three cheek patterns, and six body marks. Combined with ten archetype palettes, that's over 50,000 possible combinations.

No external rendering service, no IPFS, no centralised API, the image lives in the contract itself.

## The ten archetypes

Every biont belongs to one of ten archetypes, assigned at mint and permanent. Each has a distinct colour palette and a personality curve in the keeper's behavioural model.

| # | Archetype | Colour | Personality |
|---|---|---|---|
| 0 | VAGRANT | Cyan | Explorer; low aggression |
| 1 | VECTOR | Blue | Connector; high convergence |
| 2 | EMISSARY | Green | Ambassador; signal-heavy |
| 3 | SEEKER | Lime | Wanderer; high wander rate |
| 4 | BASTION | Amber | Defender; hardening-focused |
| 5 | BROKER | Orange | Trader; high transaction activity |
| 6 | REAPER | Red | Aggressive hunter |
| 7 | WRAITH | Purple | Stealth; low profile |
| 8 | CIPHER | Pink | Cryptographer; reputation-focused |
| 9 | VOLT | Deep orange | Erratic; high entropy |

Higher-numbered archetypes (REAPER, WRAITH, CIPHER, VOLT) carry higher base aggression in the keeper's hunt probability model.

## Permanent death

When a biont is killed via `hunt()`, `is_alive` flips to 0 and `death_epoch` is stamped. The biont's contract still exists (history is preserved forever) but it can never wander, earn, fight, or vote again.

Death triggers an on-chain cascade:

1. Tribute flows stop.
2. `auto_succession()` runs if the biont is a patriarch, the strongest child (by wander count) becomes the new patriarch.
3. Accumulated score becomes claimable essence for visitors and lineage.
4. Shareholders can redeem the pool proportionally.
5. Any open death market on this biont resolves `is_alive = 0`.
6. Claimed territory becomes available for new holders.
7. Relevant institution offices (Archivist, Registrar, Magistrate, Militia, Physician, Herald, TaxCollector, Welfare) log the event as part of their civic role.

Every kill reshapes the network's social and economic structure.
