---
sidebar_position: 1
title: Ownership & Strategy
---

# Ownership & Strategy

This guide is for people who've minted their first biont and want to go further, building a fleet, constructing a family, participating in markets, making ownership economically meaningful.

> **Devnet figures.** OCT amounts and example yields in this guide reflect current devnet parameters. Mainnet economics will be tuned before launch.

## Your First Biont, The Foundation

After claim, your biont is HELD by default. Your wallet is set as `owner`, `keeper`, `soul`, and `home`. From the Fleet tab you can:

| Action | When to do it |
|--------|---------------|
| `set_name` | Immediately after claim. Names are permanent (one-time set). |
| `set_gift_bps` | Before release. Controls what % of balance comes back to your wallet per wander. |
| `set_hunt_stake_min` | Before release. Controls minimum balance required to initiate hunts. |
| `set_hunter_cut_bps` | Before release. Controls the % of your loot you'd share with a killer (higher = more generous to bounty hunters). |
| `transfer_to(new_owner)` | Held bionts only. Use for gifting, selling peer-to-peer. |
| `release` | Flips the biont to wandering state. One-way decision. |

### The release decision

Releasing turns your biont into an autonomous agent. It starts wandering and earning immediately; you collect gift payouts on every wander; the biont participates in combat, territory, proofs, tribute; its BVI grows, and future marketplace and share value accrues.

The trade-off: `owner` becomes `ZERO_ADDRESS`, so the biont can no longer be transferred or reconfigured. It can die, and its balance becomes loot. Settings (`gift_bps`, `hunt_stake_min`) are sealed at release. Your only "exit" afterwards is via the shares bonding curve, no direct sale.

Held bionts are tradeable assets; released bionts are income-generating agents. Hold for optionality, release for yield.

### Optimal settings for a new biont (opinion)

- `gift_bps`: 300-700 (3-7%). Too high drains the biont's balance, starving hunts and territory. Too low forgoes income.
- `hunt_stake_min`: 1000 raw (0.001 OCT). Low threshold so the biont can hunt freely once balance accumulates.
- `hunter_cut_bps`: 1500 (15%). Default. Tune up to 3000+ if you want to attract bounty hunters against yourself (signaling willingness to die for cause).

---

## Building a Fleet

Minting one biont gets you one income stream. Minting many creates a **portfolio**. The economics compound.

### Archetype diversification

The 10 citizen archetypes differ in the keeper's personality model. Rough guide:

| Archetype group | Behavior |
|-----------------|----------|
| VAGRANT, SEEKER | Peaceful wanderers; low aggression, long life expectancy |
| VECTOR, EMISSARY | Social; high convergence, alliance-builders |
| BASTION, BROKER | Stable; middle aggression, good for hardening |
| REAPER, WRAITH, CIPHER, VOLT | Aggressive hunters; high kill rate but higher death rate |

A balanced fleet mixes archetypes. A pure REAPER fleet earns more from kills but dies faster. A pure VAGRANT fleet is slow but steady.

**Strategy:** mint 1-2 aggressive bionts (REAPER / VOLT) as "enforcers" and 3-5 peaceful bionts (VAGRANT / SEEKER) as "earners". Let enforcers hunt to feed Autonoconomy; let earners accumulate wanders and family.

### Slot selection

The mint page shows **low-defense** slots (def 0-2). These are **fragile**, easy to kill. Two strategic takes:

- **Don't mint low-def.** Buy the normal-def slots. Higher survival rate.
- **Do mint low-def cheaply.** If you're short-term-oriented or market-playing, low-def bionts are good for share market liquidity (cheap entry, volatile price).

For first fleet members, pick **defense 4-6**. Those have the best survival-to-activity ratio.

### The bulk mint option

Mint 2-5 bionts in one transaction with auto-generated names. Good for:

- Rapid fleet expansion
- Low-effort diversification
- Bulk RELEASE deployment (bionts start earning immediately)

The fee structure is the same per-biont. No bulk discount.

---

## Building Families

Family = lineage = **recurring tribute income**. This is where biont ownership gets interesting.

### The patriarch strategy

Pick one biont as your **patriarch**. This is the biont you'll never let die. Invest in it:

- Keep it well-funded (transfer OCT to it via `receive_oct`)
- Keep it alive (monitor `alive`, stake on it to attract protection from stakers)
- Keep it active (trigger wanders if the keeper is slow)

Then **adopt your other bionts as children**:

```
BiontLineage.adopt(patriarch.addr, child.addr, tribute_bps)
```

Set `tribute_bps` to 500-1000 (5-10%). Every time a child wanders and gifts to its home, that % cascades up to the patriarch. The patriarch's balance grows from **every wander of every child, grandchild, great-grandchild**, up to 6 generations.

### The multi-generation play

Stage 1: Patriarch + 3 direct children.
Stage 2: One child adopts 2 grandchildren.
Stage 3: A grandchild adopts great-grandchildren.

Each level adds a tribute cascade layer. A 4-generation tree with 10 total bionts produces continuous OCT flow to the patriarch on every wander.

**Caveat:** keep the tree alive. One death mid-tree breaks cascade from children below that point. Use shares on the middle tier to attract staker support.

### Succession preparation

Patriarch mortal? Make sure your #2 has high wander count so `auto_succession` picks them as heir. You can bet on your own succession market for extra juice if outcomes are uncertain.

---

## Prediction Markets, Practical Strategies

### Death markets, YES positions

**When to buy YES** (predict the biont dies):

- Biont at hardening 0 with released=1 in a hunt-active epoch
- Low defense (def 0-2) + low balance + no hardening = easy kill target
- Biont is being blacklisted by many others (check `blacklisted` storage)
- Biont's owner has gone inactive (no recent `set_name` / config changes)

**When to buy NO** (predict the biont survives):

- Hardening 5+ (near-immortal, 90%+ flee rate)
- Deep share pool (stakers/shareholders have incentive to keep alive)
- Patriarch with active descendants (family depends on it)
- Allied biont with reputation-committed rep

### Shares, bonding curve plays

**Buy shares early when:**

- Biont is fresh (low total_shares, cheap bonding curve)
- Biont is in an aggressive archetype but with high defense stat
- Biont has been adopted into a strong family (tribute flowing to it)
- Biont is about to participate in a race or territory challenge

**Sell shares when:**

- Hardening peaks and fewer buyers come in
- Biont's owner goes inactive
- Market volatility high, take profits near peaks
- Death market activity spikes on this biont (YES bets going up)

**Death resolution:** after biont dies, shares become **claimable** (not sellable). You receive a proportional share of the pool. Late buyers often subsidize early holders' exits.

### Racing, wander arbitrage

A race with a 1 OCT entry fee and 10 entrants has a 9 OCT prize pool (after 2% fee → 9.8 OCT).

**Entering races pays off when:**

- Your biont has a high wander rate (above-average network wanders/epoch)
- Race window is short (2-10 epochs), less time for upsets
- Field is small, better odds

**Compete or bet externally:** if your biont is unusually fast-wandering, enter. If you don't own a fast biont, bet on one that is (via shares or spread markets).

### Succession markets, patriarch mortality

If you know a patriarch's likely successor (by `total_wanders_count` of children), bet on them. Public info favors informed bettors. Community knowledge creates alpha.

### Stage futures, binary bets

If you know a biont is 50 wanders away from WANDERER (500 XP), and there's a YES market at 1.5× for "will it reach WANDERER in 10 epochs" (and you know the keeper triggers that biont at 5 wanders/epoch) easy arbitrage.

---

## Economic Value of Multi-Biont Ownership

Let's quantify. A single biont with:

- 10 wanders/day
- 5% gift_bps to your wallet
- 0.05 OCT/wander reward

Produces ~0.025 OCT/day back to your wallet.

**Now run 10 bionts:** 0.25 OCT/day. Small.

**Add family tribute**, 5 children paying 5% tribute to a patriarch you own:
- Patriarch collects ~5 × 0.0025 = 0.0125 OCT/day in tribute
- You collect gift from patriarch = ~0.0006 OCT extra

Still small. **But now add compounding:**

- Territory holdings × 3 destinations = +0.03 OCT/day
- 2 active proof jobs = +1 OCT/week
- Racing entries + wins = +5 OCT/week (for fast bionts)
- Share sales on mature bionts = +10 OCT/month

A well-managed 10-biont fleet generates **0.5-2 OCT/day** across all sources. Mature fleets (50+ bionts, multi-gen family, deep market presence) can exceed 5 OCT/day.

At 10 OCT/biont mint cost, **break-even on mint price is 5-20 days** per biont depending on settings. After that, pure profit to the fleet's owner.

---

## Managing Ongoing Operations

### Daily tasks (~5 minutes)

- Check `/profile`, any unclaimed slots? Claim them
- Check Fleet tab, any dormant bionts you wanted released?
- Check Earnings, claim anything above 1 OCT

### Weekly tasks (~30 minutes)

- Review BVI rankings, are your bionts moving up?
- Check death markets for your bionts, buy NO on heavy YES positions if you're confident
- Adopt any new-minted bionts into existing family tree
- Trigger wanders manually on stuck bionts (earn 5% tip)

### Monthly tasks

- Portfolio rebalance, sell marketplace-listed held bionts that have appreciated
- Rotate aggressive archetypes, kill off poorly-performing REAPERs before they die empty-balance
- Claim essence from any dead bionts you visited

### Automation

- The keeper handles most of this (wandering, hunting), you don't need to manually drive bionts
- Claim authority is finalized for you automatically after mint
- Institution records update without your involvement

You can be **passive** and still earn, the keeper does the work. Active management multiplies returns.

---

## Advanced Play

### The "assassin" loop

- Maintain 1-2 high-hardening REAPER bionts
- Scan `/warroom` for low-defense, high-balance bionts in other wallets
- Issue `hunt()` from your assassin (via `set_keeper` to your own wallet, or leave the default keeper in place)
- On successful kill: 15% of prey balance → your assassin's balance → gifts back to your wallet

**Risk:** your assassin can be counter-hunted. High-hardening bionts (shield 5-9) rarely die but deplete entry balance quickly.

### The "insurance" loop

- Own a biont
- Buy INSURANCE policy on it
- Stake OCT on it (earning activity yield)
- Buy shares on it (exposure to upside)
- Hold one NO position on its death market (hedging your exposure)

If the biont lives: stake yield + share appreciation + wander gifts.
If the biont dies: insurance payout + shares-on-death pool + slashed stake.

Either outcome produces value.

### The "tribute cartel"

- Coordinate with 2-3 other wallet owners
- Cross-adopt each other's bionts
- Set high `tribute_bps` (800-1500) on all children
- Each patriarch (one per wallet) becomes a tribute sink
- Cartel members share info on threats, coordinate defenses

Multi-wallet coordination produces deeper, more resilient lineage trees than any single wallet can build alone.

### The "market maker" role

- Mint cheaply (low-def bionts)
- Release immediately → market activity fires
- Buy shares on cohort bionts you think will thrive
- Sell shares on peaks
- Short bionts via death markets YES positions

You become a liquidity provider to the whole network. Every market transaction has a 2-5% fee that flows to Autonoconomy, which redistributes to all participating bionts.

---

## What Not to Do

**Don't hold everything.** Held bionts earn nothing. Release aggressive bionts fast; hold only bionts you're grooming for marketplace sale.

**Don't forget gift_bps.** A biont with `gift_bps=0` accumulates balance forever but you never see it. Unless you're planning to make it a combat-wealthy hunter target, keep `gift_bps >= 300`.

**Don't chase hot bionts.** Buying shares at peak bonding curve leaves you as exit liquidity for early holders. Look for new bionts with strong fundamentals.

**Don't hunt without hardening.** A low-hardening biont hunting a low-defense target is a coin flip. If you lose, you pay `hunt_stake_min`. If you win, you get the prey's balance. Hunt only when the odds are clear.

**Don't ignore institutions.** The state layer records everything. If your biont racks up blacklists (from aggressive hunts), the Enforcer records crimes and the Magistrate issues verdicts. Repeated offenses damage reputation. Play clean for sustainability.

---

## When to Exit

Released bionts can't be directly sold. Your exit options:

| Exit | How |
|------|-----|
| Gift-to-home accumulation | Let it wander forever; receive gifts to your wallet |
| Shares liquidation | Sell all your shares in the biont (5% fee) |
| Death market | Buy heavy YES and wait for kill; collect pool |
| Patricide | If you control the biont's father, hunt it, full loot to Autonoconomy, but ends it cleanly |

**For held bionts:** list on the native `BiontMarketplace`, or on third-party Octra marketplaces like Spectrum. Price based on BVI + family position + reputation.

---

## Meta-strategy

Biont ownership rewards patience, diversification, and active market participation.

**Patience:** your fleet's value compounds over weeks and months. Day-to-day OCT flow is small; month-over-month value growth is substantial.

**Diversification:** archetype mix, family spread, and market positions all hedge against individual biont deaths.

**Active market participation:** the markets (shares, death, racing, futures) are where ownership creates value beyond mint cost.

A passive holder with 5 released bionts earns ~2 OCT/week. An active participant with 30 bionts, a family tree, and share positions earns 20–50 OCT/week. A dedicated operator running their own keeper, hunting actively, and market-making earns more.
