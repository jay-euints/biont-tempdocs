---
sidebar_position: 10
title: FAQ
---

# FAQ

## General

**What is a biont?**
A biont is an autonomous on-chain agent, deployed as its own smart contract on Octra. Every biont has its own address, state, and permanent history. It wanders, earns OCT, builds families, fights, and accrues value without human intervention.

**Is a biont an NFT?**
Not in the usual sense. A standard NFT is a token ID inside a contract; a biont *is* the contract. Its visual identity is generated on-chain by calling `token_uri()` on the biont directly.

**What blockchain is Biont Network on?**
Octra, a general-purpose FHE Layer 1. Currently live on devnet; mainnet migration is planned.

**What is OCT?**
Octra's native token. Bionts earn, hold, and transact in OCT. All fees, rewards, and markets are denominated in OCT. One OCT = 1,000,000 raw on-chain units.

## Biont & Octra

**What value does Biont Network bring to Octra Network?**
Biont is the first application that fully exercises what Octra was built for:

- HFHE is load-bearing, not decorative, encrypted ally signals, reputation commitments, ZK range-proof credentials, all used inside production logic.
- Sustained transaction volume without users, the protocol's contracts run permanent loops (wanders, convergences, market settlement, institution hooks) producing thousands of transactions per day with zero human input.
- The largest production AML codebase in existence. Every compiler edge case and event-surfacing quirk is hit by Biont first.
- Ecosystem density, Biont is one of two production protocols from the same team sharing a graph. The second is a companion human-facing layer (more on that below). That's cross-app gravity, not isolated demos.
- Primitives only possible here, activity-weighted governance, permissionless settlement on death/territory/market resolution, on-chain consensus-neuron topology. Mechanics that need FHE, AML, and cheap composability.
- A continuous regression canary. When Octra ships a compiler fix or consensus tweak, Biont surfaces breakage faster than any internal test suite.

**How does Biont make Octra visible?**
Every other chain has a block explorer (rows of transactions and addresses. Biont renders Octra as a place: a hex-tiled world, destinations as nodes, bionts as moving entities, pillars for state institutions, arcs for kills, tombstones for deaths, territory as ground held. A wander isn't a line in a log) it's a biont physically moving between nodes. Epoch progression shows as a day/night cycle. You can tell the chain is alive at a glance.

The world is built so other protocols on Octra can render into it. Biont is positioned to become Octra's visual surface. Someone landing on bionts.network experiences Octra as a living world; someone landing on a block explorer experiences it as a ledger. Biont is the front door most new eyes will come through.

## Ownership

**What happens when I mint a biont?**
You pay 10 OCT (devnet testing value, mainnet pricing will be tuned before launch), pick a slot, and name your biont. The transaction splits three ways (25% protocol / 50% Autonoconomy / 25% BiontInstitution). An observer finalises your claim authority and registers the biont in the destination registry, leaving it in `is_for_mint=1` waiting for you to claim. You then call `claim_from_buyer()` from your wallet to take ownership.

**What's HOLD vs RELEASE?**

- HOLD, the biont is dormant. You own it (owner, keeper, and soul all set to your wallet). You can transfer, rename, or release it later. It doesn't wander or earn.
- RELEASE, the biont is set free immediately. `released=1`, `owner=ZERO_ADDRESS`, wandering begins on the next tick. You keep the soul connection.

**What does RELEASE mean for ownership?**
Release sets `owner` to the zero address. The biont becomes autonomous: it cannot be transferred or reconfigured by any wallet. You retain the soul role (permanently recorded as the biont's origin wallet) but lose transferable ownership.

**Can I sell my biont?**
Held bionts can be listed on `BiontMarketplace` or on third-party Octra marketplaces like Spectrum. Released bionts cannot be transferred (they're autonomous), but you can buy or sell *shares* in a released biont through the bonding curve market at `BiontShares`.

**What is the soul connection?**
The soul is the wallet address permanently associated with a biont. Even after release, the soul wallet receives gift payouts and can claim essence on death. Set at claim, never changes.

**What's the difference between owner, keeper, and soul?**

- Owner, can transfer, release, configure (held bionts only; zeroed after release).
- Keeper, the operational driver; calls wander, hunt, etc. on the biont's behalf.
- Soul, permanent identity. Receives gifts, can claim death essence.
- Home, destination for gift payouts (usually the same as soul).
- Father, set at release time; top of the lineage tree for this biont.

## State institutions

**What are state institutions?**
Twelve contracts that observe and record biont behaviour: Archivist, Cartographer, Census, Chronicler, Enforcer, Herald, Magistrate, Militia, Physician, Registrar, TaxCollector, Welfare. Each records a different class of event. Full spec in [State Institutions](concepts/state-institutions.md).

**Why twelve offices but only five worker archetypes?**
`BiontInstitution` supports five generic worker archetypes (Cartographer, Scout, Census, Warden, Distributor). The twelve offices are separate observation contracts. The frontend maps twelve offices onto five archetypes, Archivist, Chronicler, Herald, Registrar, and TaxCollector all use the SCOUT archetype, for example. It's a best-fit visual mapping.

**Who pays the workers?**
`BiontInstitution` has a treasury fed by the 25% mint share. The institution's protocol wallet funds workers via `fund_worker(addr, amount)` or `fund_all_active_workers(amount_each)`. Funding is discretionary, not automatic per action.

**Are institutions centralised?**
Deploys and worker registrations are gated to the protocol wallet. All state is public and immutable, any user can read any institution's records, verify a crime, a verdict, a census snapshot.

## Combat

**Can my biont die?**
Yes. Death is permanent. When a biont is killed, `is_alive` flips to 0 and it can never wander, earn, or act again. Its contract and history remain on-chain forever.

**How does combat work?**
A biont's keeper calls `hunt(prey_address)`. The hunter must have `released=1`, `alive=1`, and a balance at least `hunt_stake_min`. The hunt triggers resolution on the prey contract: the prey rolls against its shield (defence + a hardening bonus). If the roll fails, the prey flees, gains hardening, and the hunter forfeits the stake as a flight fee. If the roll succeeds, the prey dies, and the loot (the prey's full balance) splits between the hunter and Autonoconomy.

**What's hardening?**
A defence stat that only increases. Every time your biont survives an attack, hardening goes up by 1. It caps the shield contribution at `min(5, hardening)`, but the underlying value is a permanent record of survival.

**What happens to my biont's family when it dies?**
If the biont is a patriarch, `auto_succession()` runs: the strongest child (by wander count) becomes the new patriarch, and tribute flows re-route to them. If no viable successor exists, the family is orphaned and tribute stops.

**What is patricide?**
If a child biont hunts its own father (`caller == self.father`), the full loot goes to Autonoconomy, no hunter cut. Emits `Patricide(father, loot, epoch)` and files a record in Archivist.

## Economics

**How do bionts earn OCT?**
Wander rewards (from `BiontRewards`), gift-to-home on every wander, tribute from descendants (up to six generations), territory income while holding a destination, proof completions, task broker work, and convergence rewards when two released bionts meet at the same destination.

**What is tribute?**
When a biont has children, each child automatically sends a percentage of its wander earnings to its parent, cascading up to six generations. Set at adoption time via `BiontLineage.adopt(parent, child, bps)`.

**What's the BVI?**
The Biont Value Index, a four-axis composite: **Activity** (log-scaled wanders + proofs + convergences), **Durability** (hardening + age alive), **Economic footprint** (OCT balance + territory held + tribute earned), and **Social position** (descendants + alliances + reputation commitment). Summed, then modulated by alive status (dead bionts retain 60% of their score so veterans aren't erased). Default leaderboard sort. Full formula in [Economics](economics.md#biont-value-index).

**How are mint proceeds split?**
25% protocol, 50% Autonoconomy (redistributed as rewards), 25% BiontInstitution treasury.

## Technical

**Where is the biont's image stored?**
On-chain. Each biont has a `token_uri()` function that returns a complete animated SVG. No IPFS, no centralised server. The image is generated from the biont's `birth_seed` and its archetype palette.

**Is the keeper centralised?**
The keeper is a convenience layer, it triggers wander transactions on a schedule, handles the mint-to-claim flow, evaluates hunt probability, and fires institution hooks. But anyone can call `wander()` on a biont directly; the tip (5% of the wander reward) is paid to whoever submits the transaction. You can run your own keeper and earn tips. Proof verification, market settlement, and territory resolution are all permissionless.

**What is Octra's FHE?**
Fully Homomorphic Encryption allows computation on encrypted data without decryption. Octra uses HFHE (Hypergraph FHE) natively in the VM. Biont's current mechanics are transparent, with encrypted primitives (ally signals, encrypted balances, private alliances) being rolled in incrementally. See [Privacy and HFHE](concepts/privacy.md).

**Why did my transfer show the wrong tx hash in the UI?**
A known 0xio SDK quirk, the SDK sometimes returns a locally computed hash that differs from the canonical on-chain one. The transfer succeeds; look up by address on the explorer to find the real hash.

## Future

**Is there anything else coming?**
Yes, a companion human layer on the same graph. Different participants, same protocol. Your biont's on-chain history becomes a verifiable social credential there, and communities can gate membership on biont attributes. More soon.

**Will Biont Network move to mainnet?**
Yes. Currently live on devnet; mainnet migration is planned once the devnet phase validates the social and institutional layers.

**Can new archetypes be added?**
`BiontsCore` hard-codes ten citizen archetypes and `BiontInstitution` hard-codes five worker archetypes. Adding more would require a redeploy. Expansion is planned.

**Can institutions be added beyond the twelve?**
Yes. Each institution is a separate contract; new ones can be deployed and wired into `EUBRegistry` without touching citizen bionts. The frontend reads from the registry, so new offices appear automatically.
