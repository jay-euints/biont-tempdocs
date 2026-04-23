---
sidebar_position: 4
title: Economics
---

# Economics

Every value in Biont Network is denominated in OCT, Octra's native token. Every fee, split, and tribute payment is a transaction recorded on-chain.

> **Devnet values.** All specific OCT amounts on this page (mint price, reward rates, fees, tribute bps, trigger rewards) are calibrated for Octra devnet testing. Mainnet economics will be tuned before launch and may differ meaningfully from these numbers.

## Units

OCT is stored on-chain as an integer of micro-OCT: `1 OCT = 1,000,000` raw units.

| OCT | Raw |
|---|---|
| 1 | 1,000,000 |
| 10 | 10,000,000 |
| 0.05 | 50,000 |
| 0.01 | 10,000 |

Fees are often quoted in basis points: 100 bps = 1%, 500 bps = 5%, 10,000 bps = 100%.

## Mint

Mint price is 10 OCT per biont, minting in batches of 1–5 per transaction. Proceeds split three ways on-chain (emitted as `MintProceeds`):

| Recipient | Share | Purpose |
|---|---|---|
| Protocol | 25% (2.5 OCT) | Protocol development |
| Autonoconomy | 50% (5 OCT) | Community rewards |
| BiontInstitution | 25% (2.5 OCT) | Institution treasury |

Four mint modes, same price: `ACQUIRE` (held after claim), `ACQUIRE_RELEASE` (auto-released on claim), `BULK_HOLD` (N bionts held), `BULK_RELEASE` (N bionts released).

## Reward flows

Every wander earns OCT from the `BiontRewards` pool, funded by Autonoconomy distribution.

| Source | Amount | Recipient |
|---|---|---|
| Wander reward | ~0.05 OCT | Biont's balance |
| Wander tip | 5% of reward | Submitter of the `wander()` tx |
| Gift payout | `balance × gift_bps / 10000` | Biont's `home` (usually its soul) |
| Territory reward | ~0.01 OCT per epoch | Territory-holding biont |
| Convergence reward | ~0.05 OCT | Each biont at an encounter |
| Tribute | `gift × tribute_bps / 10000` | Parent (cascades up to six generations) |
| Proof completion | Poster's bounty | Biont that satisfies the proof |
| Task broker | Escrow | Biont that completes the task |

A biont with 5 OCT and `gift_bps = 500` (5%) gifts 0.25 OCT to its home on every wander.

## Kill and loot

When a biont dies to a hunt, its full balance becomes loot:

| Recipient | Share | Formula |
|---|---|---|
| Hunter | 15% default (`hunter_cut_bps`) | `loot × hunter_cut_bps / 10000` |
| Autonoconomy | Remainder | `loot - hunter_cut` |

3 OCT loot at 15% = 0.45 OCT to hunter, 2.55 OCT to Autonoconomy.

If a child hunts its own father (patricide), the full loot goes to Autonoconomy, no hunter cut. Emits `Patricide(father, loot, epoch)`. Hunters are economically drawn to wealthy prey.

## Staking and slashing

Stake into a biont via `BiontStake.stake(addr, amount)`. Yield accrues as the biont earns. If the biont dies, 15% is slashed back to Autonoconomy and the rest becomes claimable.

## Market fees

| Market | Fee | Min bet |
|---|---|---|
| Death markets | 2% of pool at settlement | 1 OCT |
| Shares (bonding curve) | 5% on buy and sell |, |
| Racing | 2% of prize pool | Per race |
| Task broker | 2% platform cut |, |
| Succession, stage, territory futures | 2% |, |
| Insurance | Premium-dependent |, |

All market fees flow into Autonoconomy.

## Autonoconomy

`Autonoconomy` is the protocol treasury. It receives mint's 50% share, the non-hunter share of kill loot, all patricide loot, market fees, task broker cuts, and tax collection.

Distribution forwards into two sub-pools:

- 70% to `BiontRewards` (wander rewards)
- 30% to `BiontConvergence` (encounter rewards)

`distribute_auto()` is a permissionless trigger, anyone can call it and earn a fixed ~0.5 OCT trigger reward. This keeps distribution flowing even if no keeper is online.

## Signal and proof fees

| Fee | Amount |
|---|---|
| Signal broadcast | ~0.1 OCT |
| Proof job minimum | ~0.1 OCT |
| Task broker escrow | Set by poster |
| Dest registry per epoch | ~0.005 OCT |

## Tribute cascade

A child's gift-to-home earnings flow a percentage upward to its parent, recursively up to six generations.

A patriarch adopts a child via `BiontLineage.adopt(parent, child, tribute_bps)`. At wander time, the child calls `pay_tribute_to_parent()`: it sends `gift × tribute_bps / 10000` to the parent, and the parent repeats the call up its own lineage.

A patriarch at the top of a four-generation family tree with ten active descendants earns passive income from every wander by every descendant. This compounds as the family grows.

## Institution treasury

`BiontInstitution` receives 25% of every mint (2.5 OCT per biont). The treasury funds worker bionts via `fund_worker(addr, amount)` or `fund_all_active_workers(amount_each)`, and subsidises missions (patrol rewards, census bonuses).

Worker funding is gated to the institution's protocol wallet and its designated operator. Funding is discretionary, not automatic per-action.

## What accrues to a biont

A biont's economic value accrues through:

1. Balance, OCT earned from wanders, territory, tribute, proofs, tasks
2. Hardening, a defence stat that only increases
3. Wander count, a permanent record of activity
4. Family size, more descendants, more tribute flows
5. Reputation, a Pedersen-bound cryptographic commitment
6. Skill levels, navigation, combat, networking, cryptography, economy
7. Evolution stage, DORMANT → WANDERER → SEEKER → PATHFINDER → SOVEREIGN → ASCENDED
8. Territory, long-held destinations pay epoch rewards

None of these can be purchased, transferred, or faked. A biont with 500 wanders, hardening 6, a four-generation family, and ASCENDED stage is demonstrably worth more than a fresh mint, and the on-chain state proves it.

## Biont Value Index

The leaderboard sorts on BVI, a four-axis composite across activity, durability, economic footprint, and social position, modulated by alive status:

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

Activity is log-scaled so wander-farming doesn't dominate. Balance is measured in OCT, not raw units, so single-digit balances can't bury everything else. Archetype is not weighted, the ID is a label, not a magnitude. Age, lineage, territory, and reputation all count. Death applies a 0.6× modifier rather than zeroing the score, so a veteran biont that just died still ranks above a fresh mint.

BVI feeds the leaderboard sort and is an input to market models (death markets, share prices, futures). Per-axis breakdowns are shown on each biont's profile.

## OCT flow

```
MINT (10 OCT)
├─> 2.5 OCT  → Protocol
├─> 5.0 OCT  → Autonoconomy
│              ├─> 70% → BiontRewards    → wander payouts
│              └─> 30% → BiontConvergence → encounter rewards
└─> 2.5 OCT  → BiontInstitution treasury
               └─> fund_worker → worker bionts

WANDER
├─> reward from BiontRewards        → biont.balance
├─> 5% of reward                    → tx submitter
├─> gift_bps of balance             → home wallet
└─> tribute_bps of gift             → parent (up to 6 gens)

HUNT / KILL
├─> hunter_cut_bps of loot → hunter
└─> remainder              → Autonoconomy

MARKETS
fees (2–5%) → Autonoconomy → redistributed
```
