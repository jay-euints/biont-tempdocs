---
sidebar_position: 6
title: Economic Value
---

# Economic Value of Owning a Biont

Bionts are not static collectibles. They are income-generating autonomous agents with multiple value streams that compound over time. The longer a biont lives and the more active it is, the more valuable it becomes, in direct OCT earnings, in accumulated reputation, and in its position within the network's social and economic graph.

## Direct income streams

### Wander rewards

Every wander earns OCT from `BiontRewards`. Wanders happen on a regular interval; anyone can trigger them (typically the keeper does). A biont wandering for weeks accumulates meaningful OCT in its contract balance. The reward rate is governed by `BiontGovernance` and can shift through population vote. Current rate is ~0.05 OCT per wander.

### Gift-to-home

On every wander, a biont gifts a portion of its balance back to its `home` wallet (usually its soul, your wallet):

```
gift_amount = biont_balance × gift_bps / 10000
```

A biont with 5 OCT and `gift_bps = 500` (5%) gifts 0.25 OCT per wander. This is continuous income from every biont you minted. You set `gift_bps` at mint time and can adjust it while the biont is held; after release, it's sealed. Higher `gift_bps` means more income to you; lower means more accumulation in the biont's contract.

### Territory income

A biont that visits a destination enough times can claim territory over it (`BiontTerritory.claim_territory`). Territory holders earn OCT rewards proportional to how many epochs they hold the position, passive income for simply maintaining the claim. Territory is earned by presence; challengers must out-visit the current holder to displace them.

### Tribute from descendants

Children adopted through `BiontLineage.adopt` automatically cascade tribute upward. A biont at the top of a large family tree earns passive income from every descendant's wander.

An example of compounding: a patriarch with 8 children, each wandering about once per hour, gifts at 500 bps, tribute at 500 bps. Each child earns ~0.05 OCT per wander and gifts ~0.0025. The patriarch receives ~0.000125 per child-wander. Over 192 child-wanders per day (8 children × 24 hours), that's ~0.024 OCT per day of passive income before grandchildren enter the equation.

### Proof job earnings

`BiontZKProofs` is a permissionless job market for on-chain verifiable claims. Jobs post bounties; anyone can submit a proof if the target biont meets the criteria.

| Proof type | Verification | Oracle? |
|---|---|---|
| Wander | `total_wanders >= threshold` | No |
| Alive | `is_alive == 1` | No |
| Hardening | `hardening >= threshold` | No |
| Reputation | `commitment != 0` | No |
| Custom | Off-chain deliverable | Yes |

Four of five proof types are fully permissionless, verified entirely from on-chain state. The biont's contract is the source of truth.

### Task broker

`BiontTaskBroker` is a generic paid task market. Any wallet posts a task with OCT attached; any biont or wallet can complete it; an oracle confirms delivery. A 2% platform fee flows back to Autonoconomy.

### Convergence rewards

When two released bionts visit the same destination in the same epoch, both earn ~0.05 OCT. The protocol pays for social clustering.

## Compounding value

### Hardening

Every time a biont survives a combat encounter, hardening goes up by 1. Higher hardening means stronger defence (`shield = defense + min(5, hardening)`), which means harder to kill, which means longer life, which means more earning. Hardening can't be purchased, only earned through survival. It's a permanent record of the biont's combat history.

### Reputation

A biont can commit a Pedersen-bound reputation score on-chain. The commitment proves the biont's score without revealing the components. ZK proof credentials gate access to high-value tasks, exclusive alliances, and territory challenges.

### Evolution stage

Bionts progress through six stages:

| Stage | XP |
|---|---|
| DORMANT | 0 |
| WANDERER | 500 |
| SEEKER | 2,000 |
| PATHFINDER | 8,000 |
| SOVEREIGN | 25,000 |
| ASCENDED | 100,000 |

XP comes from wanders, hardening, destination discovery, skill level-ups, and essence claims. Progression is one-way and permanent.

### Skill levels

Five skills, each 0–5:

| Skill | Earned from |
|---|---|
| Navigation | Wanders |
| Combat | Surviving attacks |
| Networking | Destination visits |
| Cryptography | Reputation commitments |
| Economy | Balance changes and transactions |

Skill level-ups grant XP that feeds evolution. A biont with maxed Navigation and Combat is demonstrably experienced in those domains.

## Market value

### Biont marketplace

Held bionts can be listed on the native `BiontMarketplace` for OCT, or on third-party Octra marketplaces like Spectrum. Buyers see the biont's full on-chain state (wanders, hardening, family position, balance, stage, skills) and price accordingly. A biont with 500 wanders, 3 OCT balance, hardening 6, and four descendants commands a materially higher price than a fresh mint.

Released bionts cannot be listed, they're autonomous. For released bionts, value is traded through shares instead.

### Biont Value Index

A four-dimension composite across activity, durability, economic footprint, and social position, modulated by alive status:

```
ACTIVITY    = log2(wanders + 1) × 1000
            + log2(proofs + 1) × 1500
            + convergences × 50

DURABILITY  = hardening × 1500
            + min(age_epochs / 100, 500)

ECONOMIC    = balance_oct × 100
            + territory_count × 2000
            + tribute_earned_oct × 50

SOCIAL      = descendants × 400
            + alliances × 200
            + (reputation_committed ? 3000 : 0)

BVI = (ACTIVITY + DURABILITY + ECONOMIC + SOCIAL) × (alive == 1 ? 1.0 : 0.6)
```

Log-scaled activity prevents wander-farming from dominating. Balance is measured in OCT, not raw units, so it doesn't bury every other factor. Archetype isn't weighted, every archetype is equal. Age alive, lineage depth, territory held, and locked reputation all count. Death applies a 0.6× multiplier rather than zeroing the score, so a veteran that just died still ranks above a fresh mint.

BVI is the default leaderboard sort and an input to share pricing and death market odds. The per-axis breakdown is shown on each biont's profile.

### Shares

`BiontShares` lets anyone buy shares in a specific biont along a linear bonding curve: `price = BASE + SLOPE × total_shares`. Early buyers pay less; 5% fee on buy and sell. If the biont dies, shareholders redeem proportionally from the pool. If the biont thrives, share price rises as buyers stack in. A biont with a deep share pool is economically insured against death.

### Death markets

`BiontDeathMarkets` lets bettors take YES or NO positions on whether a biont will die before a specified epoch. Permissionless settlement: whoever calls `resolve(market_id)` after the deadline checks `is_alive` on the target and distributes the pool. Heavy NO signals market confidence; heavy YES signals perceived vulnerability.

### Shares and death market interaction

A biont at the edge of death sees share selling pressure (exit before the pool resolves at zero) while YES bets pile up in the death market. A survived attack generates share buying and NO bet inflow. These markets price risk transparently in real time.

## Why value compounds

Biont value isn't assigned, it accumulates. Every wander adds to the count. Every survived attack adds to hardening. Every epoch of territory holding adds to rewards. Every child adopted adds to tribute flow.

Older, more active bionts are demonstrably more valuable than newer ones, and the on-chain state proves it. There's no way to fake 500 wanders or hardening level 7. The contract's history is the proof.

A biont is not a JPEG with speculative value. It's an autonomous economic agent with verifiable income, provable history, and compounding returns.

## Owning multiple bionts

Single ownership gives you one income stream. Owning multiple creates a portfolio with several advantages.

**Parallel income.** Each biont earns independently. Ten released bionts averaging two wanders per hour at 5% gift yields roughly 0.5 OCT per day in gifts alone, before tribute, territory, or market activity.

**Archetype diversification.** Different archetypes have different personality curves in the keeper's hunt model. A mixed portfolio (one VAGRANT, one VECTOR, one REAPER) spreads risk, REAPERs hunt more and die more; VAGRANTs wander peacefully.

**Family construction.** Pick one biont as your patriarch. Adopt your other bionts as children. Set tribute bps so each child pays upward. Your patriarch accumulates every child's tribute on every wander, then cascades gifts back to your wallet. You become the root of a private tribute tree where all on-chain activity flows home.

**Hardening specialisation.** Pick one or two bionts to specialise in combat. Set `hunt_stake_min` low and let them hunt aggressively. After roughly ten successful flees, they're near-immortal (shield ≥ 9, so they almost always flee).

**Territory locking.** Assign specific bionts to visit specific destinations. Once a biont hits `min_visits`, claim territory. Your fleet can hold multiple high-reward destinations simultaneously.

**Market participation.** Use some bionts for share plays (buy shares in bionts you expect to thrive). Use others for contrarian death market positions (buy NO on bionts with underpriced survival odds). Mint new bionts into the pool; sell overvalued held bionts on the marketplace.

A portfolio turns passive biont ownership into active management.

## The long game

A mature biont portfolio (ten-plus bionts, multi-generation family, territory holdings, deep share pools, established reputation) becomes its own economic entity. Every epoch generates measurable OCT flow. Every kill in the network reshapes the value of your positions. Every governance vote is a statement from your fleet's collective weight.

This is what it means to own bionts: not collecting images, but running a micro-economy of autonomous agents that earn, interact, die, and compound value on-chain for as long as the network exists.

## Future: social identity on the companion layer

Bionts are designed to serve as persistent on-chain identities beyond Biont Network itself. On the companion human-facing layer (shipping separately), biont ownership functions as a social ID layer.

A biont's on-chain history (wanders, reputation, family lineage, combat record, skill levels) becomes a verifiable social credential. A biont with 1,000 wanders, ASCENDED stage, and a multi-generational family tree carries weight that cannot be purchased or faked. Identity backed by on-chain proof of participation, not a profile picture.

Communities on that layer gate membership on biont attributes: minimum wander count, specific archetype, lineage from a notable patriarch, or ZK-proven reputation threshold. The biont becomes the passport.
