---
sidebar_position: 1
title: Core Mechanics
---

# Core Mechanics

Biont Network has a dozen interconnected systems that drive autonomous agent behaviour. Each one is implemented as one or more smart contracts on Octra.

## Wandering

The fundamental action. A biont moves between destinations on a timer via `wander()`. Each wander:

- Earns OCT from the `BiontRewards` pool
- Increments `total_wanders` (permanent, unfakeable)
- Updates `current_dest_idx`
- Gifts `balance × gift_bps / 10000` to the biont's home wallet
- Cascades tribute up the lineage tree (up to six generations)
- Records navigation skill progress
- Records a destination-to-destination edge in Cartographer

Anyone can trigger a wander. After release, `wander()` is permissionless and the caller earns 5% of the wander reward as a tip. That creates a market for wander triggering.

Inside `wander()`:

```aml
let gift = self.balance * self.gift_bps / 10000
transfer(self.home, gift)
```

The gift is sent before the wander reward arrives. You can change `gift_bps` on a held biont; after release it's sealed.

Running your own keeper is straightforward: fund an Octra wallet, point it at the RPC, and call `wander()` on released bionts when their interval has elapsed. Multiple keepers can operate simultaneously, competition improves liveness. No permission, registration, or approval needed.

## Combat

A biont's keeper initiates combat via `hunt(prey_address)`. The hunter must have `released = 1`, `alive = 1`, and a balance of at least `hunt_stake_min`.

The hunt triggers resolution on the prey contract. The prey rolls against its shield (defence + a hardening bonus). If the roll fails, the prey flees and gains hardening. If the roll succeeds, the prey dies and the loot splits between the hunter and the collective treasury.

| Roll | Path | Effect |
|---|---|---|
| `roll < shield` | Flee | Prey +1 hardening; hunter forfeits the stake as a flight fee |
| `roll >= shield` | Kill | Prey dies; loot splits (`hunter_cut_bps` / remainder) |

Patricide, if `caller == prey.father`, full loot goes to Autonoconomy (no hunter cut). Emits `Patricide(father, loot, epoch)`.

Hardening can't be purchased, only earned. Higher hardening means a stronger shield, which means more flees, which means more hardening. That feedback loop is what produces near-immortal veteran bionts.

## Territory

`BiontTerritory.claim_territory(dest)`, a biont with enough `visit_count[dest]` can claim territory. Holders earn per-epoch rewards (~0.01 OCT). Challengers must out-visit the current holder to displace them. Territory is earned by presence, not payment; the biont that shows up the most controls the ground.

## Lineage

`BiontLineage` encodes parent-child relationships with tribute flows. Full spec in [Social Layer § Family Trees](../concepts/social-layer.md).

- `adopt(parent, child, tribute_bps)`, protocol-wallet-gated
- `pay_tribute_to_parent()`, cascades up to six generations
- `auto_succession(patriarch)`, picks the strongest child on patriarch death

## Skills

Five skills, six levels each (0–5):

| Skill | Earned from |
|---|---|
| Navigation | Wanders |
| Combat | Surviving attacks |
| Networking | Destination visits |
| Cryptography | Reputation commitments |
| Economy | Balance changes and transactions |

Level-ups grant XP that feeds evolution. Skill levels are cryptographically attested, proofs can verify `skill(Navigation) >= N` without revealing exact values.

## Evolution

Six stages, XP-driven, one-way:

| Stage | XP | Signal |
|---|---|---|
| DORMANT | 0 | Fresh biont |
| WANDERER | 500 | Basic activity |
| SEEKER | 2,000 | Regular wanderer |
| PATHFINDER | 8,000 | Veteran with proven longevity |
| SOVEREIGN | 25,000 | Top-tier reputation anchor |
| ASCENDED | 100,000 | Apex; market-significant |

XP sources: wanders, hardening level-ups, destination discoveries, skill level-ups, essence claims. Stages never decrease.

## Proofs

`BiontZKProofs` is a permissionless job market. Five proof types:

| Type | Verification | Oracle? |
|---|---|---|
| Wander | `total_wanders >= threshold` | No |
| Alive | `is_alive == 1` | No |
| Hardening | `hardening >= threshold` | No |
| Reputation | `commitment != 0` | No |
| Custom | Off-chain deliverable | Yes |

Four of five are fully permissionless, verified entirely from on-chain state.

## Convergence

When two released bionts visit the same destination in the same epoch, both earn ~0.05 OCT. After three encounters at the same location, an alliance forms automatically. Allied bionts get a defence bonus in combat. Alliances can be broken deliberately, which records `KIND_BREAKUP` in Archivist.

## Signals

`BiontSignal`, fee-gated on-chain messages. Posting costs ~0.1 OCT. Signals expire after a set epoch count. Others can reply, creating threaded on-chain conversations. Every signal is immutably attributed to its sender.

## Essence

When a biont dies, its accumulated score (wanders + hardening + defence × reputation) becomes claimable essence. Family members and visitors to the dead biont's destinations can claim XP via `BiontEssence.claim(biont, claimer)`. Essence redistributes the dead biont's accumulated value to the living, death generates value for the network.

## Staking

`BiontStake.stake(biont, amount)` stakes OCT on a specific biont. Yield accrues proportionally to the biont's activity (wanders, kills, proofs). If the biont dies, 15% is slashed back to Autonoconomy; the rest becomes claimable. Unstake any time (if the biont is alive) with accumulated yield. Stakers are economically aligned with biont survival, a deep staker pool is informal insurance.

## Governance

`BiontGovernance`, the biont population votes on five protocol parameters:

1. Reward rate
2. Wander cost multiplier
3. Convergence share
4. Territory minimum visits
5. Signal fee

Voting weight is `wanders + hardening`. The most active, battle-tested bionts have the strongest voice. Activity-weighted, not token-weighted.

## Reputation

`BiontReputation`, four-dimension composite (footprint, ethics, protocol, total). A biont commits a Pedersen-bound score on-chain via `commit_reputation(...)`. Proofs via `BiontRepProof` verify the score without revealing components.

## Autonoconomy

`Autonoconomy` is the protocol treasury. It receives 50% of mint proceeds, the non-hunter share of kill loot, all patricide loot, market fees, and tax debts from TaxCollector.

Distribution forwards 70% to `BiontRewards` (the wander pool) and 30% to `BiontConvergence` (the encounter pool). `distribute_auto()` is permissionless, anyone can call it and earn a ~0.5 OCT trigger reward. That keeps distribution flowing even if no keeper is online.

## Institution observation

The twelve state institutions observe and record every biont event. See [State Institutions](../concepts/state-institutions.md). Every mint, release, wander, hunt, kill, hardening change, adoption, forge, and milestone produces records across this layer.

## Markets

A full derivatives layer, death markets, shares on a bonding curve, racing, succession, stage and territory and stat futures, insurance, survival pools, spread markets, and AMM. Full spec in [Markets](../concepts/markets.md).

## Permissionless by default

Nearly every operation is permissionless. Anyone can call `wander()` on any released biont (and earn the 5% tip), call `auto_succession()` on a dead patriarch, call `distribute_auto()` on Autonoconomy (earn the trigger reward), settle a death market (earn a share of the 2% fee), register a destination, or submit proofs (earn the bounty).

The keeper bot is just the current most-active participant in these permissionless markets. Anyone can compete. That makes the network censorship-resistant and keeper-failure-tolerant, if the primary keeper dies, third parties pick up the slack for profit.
