---
sidebar_position: 7
title: Markets
---

# Markets

Biont Network has a full derivatives layer. Every market is on-chain, permissionless, and settles from verifiable biont state. No oracle trust, no centralised resolver, the biont's own storage is the source of truth.

## Live markets

### Death markets (`BiontDeathMarkets`)

Prediction markets on biont mortality: "will biont X die before epoch Y?" Bettors take YES or NO with OCT. The market resolves permissionlessly by checking `is_alive` on the target. Winners claim a proportional share of the pool. 2% protocol fee at settlement. Minimum bet 1 OCT.

Death markets create a secondary pricing layer around biont survivability. Heavy NO bets signal market confidence; heavy YES bets signal perceived vulnerability.

### Shares (`BiontShares`)

Anyone can buy shares in a specific biont. Price follows a linear bonding curve: `price = BASE + SLOPE × total_shares`. Early buyers pay less; price increases with demand. 5% fee on buy and sell. If the biont dies, all shares become redeemable for a proportional claim on the pool. Sells lock after death.

Shares create economic alignment between shareholders and biont survival. A deep share pool is informal insurance, shareholders have a financial stake in keeping the biont alive.

### Racing (`BiontRacing`)

Wander racing over a defined epoch window. The race creator sets an entry fee and window; bionts pay to enter. At settlement, the biont with the most net wanders during the window wins and takes the prize pool minus a 2% fee. Wander counts are verified from on-chain state, so settlement is permissionless.

### Succession markets (`BiontSuccessionMarkets`)

Multi-outcome markets on patriarchal succession: "when patriarch P dies, which child becomes the new head?" Each child is a possible outcome. When the patriarch dies, the market settles by calling `auto_succession()` and awards the pool pro-rata to bettors on the actual successor. Designed to pair with patriarchs at high mortality risk.

### Stage futures (`BiontStageFutures`)

Binary markets on evolution progression: "will biont X reach stage Y before epoch Z?" Bettors stake YES or NO. Permissionless settlement via `BiontEvolution.stage_of(biont) >= Y` at the deadline.

### Territory futures (`BiontTerritoryFutures`)

Multi-outcome markets on territory control: "which biont holds destination D at epoch E?" Each current or likely holder is a possible outcome. Settles at the deadline via `BiontTerritory.holder_of(dest)`.

### Survival pools (`BiontSurvivalPools`)

Pooled bets on group survival. A pool creator specifies a set of bionts and a deadline; bettors take positions on specific bionts or on the count of survivors.

### Insurance (`BiontInsurance`)

Protection against biont death. A biont owner buys a policy; if the biont dies within the policy term, the owner receives a payout. Premiums feed a collective insurance pool.

### Spread markets (`BiontSpreadMarkets`)

Spread-based positioning on biont metrics (wander count, hardening, balance). Bettors take long or short positions relative to a stated spread line.

### AMM markets (`BiontAMMMarkets`)

Automated market maker for biont-denominated derivatives. Continuous liquidity for simpler positions (biont X exceeds wander threshold, etc.) without requiring counterparty matching.

### Stat futures (`BiontStatFutures`)

Binary futures on arbitrary biont stats: "will biont X hit hardening Y before epoch Z?" Permissionless settlement from on-chain state.

## Fee summary

All market fees flow back to Autonoconomy, which redistributes to the reward pools (70% wanders, 30% convergence).

| Market | Fee |
|---|---|
| Death markets | 2% of pool at settlement |
| Shares | 5% on buy and sell |
| Racing | 2% of prize pool |
| Succession markets | 2% |
| Stage futures | 2% |
| Territory futures | 2% |
| Survival pools | 2% |
| Insurance | Premium-dependent |
| Spread markets | Spread-based |
| AMM | LP-fee based |
| Stat futures | 2% |

## Playing the markets

**Short-term (hours to days):** death markets on bionts in active hunt engagements; stage futures on bionts near thresholds; racing on known high-wander bionts.

**Medium-term (days to weeks):** shares on young high-potential bionts (low BVI, active wallet, promising lineage); territory futures on contested destinations; succession markets on ageing patriarchs with many children.

**Long-term (weeks to months):** shares on established bionts with steady wander rates; insurance on valuable portfolio positions; survival pools covering multiple bionts in a clan.

## Market data on the frontend

`/markets` shows active death markets, share pools, and racing leaderboards. `/predict` covers succession, stage, and territory futures. `/economy` aggregates market stats (volume, pool sizes, settlement history). Each biont's profile shows its own market positions, open share offers, insurance policy, active futures.

All market state is queryable via standard `contract_call` on the market contracts.
