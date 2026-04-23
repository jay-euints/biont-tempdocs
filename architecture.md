---
sidebar_position: 9
title: Architecture
---

# Architecture

Biont Network is a multi-layer protocol. Every layer is an on-chain contract written in AppliedML (AML) on Octra.

## Layers

- **Citizens**, one contract per biont. Each one is sovereign: its own address, state, and history. The canonical contract is `BiontsCore`, deployed per biont.
- **Infrastructure**, shared singletons that every citizen interacts with. Treasury, rewards, markets, registries, proofs.
- **State institutions**, twelve observation contracts that record, tax, and adjudicate. See [State Institutions](concepts/state-institutions.md).
- **Institution workers**, `BiontWorker` instances registered with `BiontInstitution`. Two workers per office, visible as staff.
- **Consensus neurons**, ten `BiontConsensusNeuron` contracts arranged in a four-layer topology (input → pattern → encode → output).
- **Registry**, `EUBRegistry` is the central address lookup every other contract registers into.
- **Bridge**, forwards biont events into the companion social layer as an observer.

## Citizens, `BiontsCore`

Every biont is its own deployed contract. The contract holds:

- Identity: `name`, `symbol = "BIONT"`, `birth_seed`, `archetype_id` (0-9)
- Ownership: `owner`, `keeper`, `soul`, `home`, `father`, `pact_partner`
- State: `alive`, `released`, `is_for_mint`, `hardening`, `defense`
- Activity: `total_wanders`, `last_wander`, `reg_count`, `visit_count`
- Economy: `received`, `generosity`, `gift_bps`, `hunt_stake_min`, `hunter_cut_bps`
- Social: `blacklisted`, `ally_signal_out`, `ally_inbox`
- FHE: `reputation_commitment`, `fhe_pubkey`

There is no central registry that "contains" bionts. Each one is addressable, callable, and queryable directly. Storage keys `name` and `symbol` are readable by the Octra explorer for OCS01 compliance.

Per-biont configuration wired at deploy: dest registry, lineage, skills, nervous system, essence, convergence.

## Infrastructure Singletons

| Contract | Purpose |
|----------|---------|
| `Autonoconomy` | Treasury and reward distribution |
| `BiontMint` | Genesis minting, slot selection, naming, four mint modes |
| `BiontRewards` | Wander reward pool |
| `BiontEvolution` | XP tracking, six-stage progression |
| `BiontConvergence` | Alliance and dominance encounters |
| `BiontLineage` | Family trees, tribute cascade, succession |
| `BiontTerritory` | Destination claims and epoch-based rewards |
| `BiontTerritoryProofGate` | Territory claim verification |
| `BiontGovernance` | Activity-weighted voting |
| `BiontSkills` | Five skills, six levels each |
| `BiontSignal` | On-chain messaging with fees and expiry |
| `BiontEssence` | Death essence recording and XP claiming |
| `BiontStake` | Staking on biont performance |
| `BiontProofs` | Proof submission and task completion |
| `BiontZKProofs` | Permissionless proof job market |
| `BiontTaskBroker` | Paid task posting and worker matching |
| `BiontBounty` | On-chain bounty system |
| `BiontMarketplace` | Held-biont trading |
| `BiontDestRegistry` | Weighted destination registry |
| `BiontRankings` | Leaderboard feeds |
| `BiontEpochLedger` | Per-epoch statistics snapshots |
| `BiontReputation` | Four-dimension reputation tracking |
| `BiontNervousSystem` | On-chain social graph |
| `BiontWardenSentinel` | Network security monitoring |
| `BiontRacing` | Wander racing markets |
| `BiontDeathMarkets` | Death prediction markets |
| `BiontShares` | Bonding curve shares per biont |
| `BiontSuccessionMarkets` | Markets on patriarch succession outcomes |
| `BiontStageFutures` | Futures on evolution stage |
| `BiontTerritoryFutures` | Futures on territory holders |
| `BiontStatFutures` | Futures on biont stats |
| `BiontSpreadMarkets` | Spread-based markets |
| `BiontAMMMarkets` | Automated market maker |
| `BiontSurvivalPools` | Pooled survival bets |
| `BiontInsurance` | Biont death insurance |
| `BiontRepProof` | Reputation proof verification |
| `BiontAdoptionGate` | Lineage adoption verification |
| `BiontProofSweeper` | Periodic proof cleanup |
| `BiontsSubstrate` | Archetype progression gating |
| `AutonoconomyScheduler` | Incentivised periodic distribution triggers |
| `EUBRegistry` | Central address registry |

## State Institutions

Twelve observer contracts running parallel to the citizen layer. Each has its own address, its own storage, and its own role.

| Office | Role |
|--------|------|
| Cartographer | Records edges as bionts wander |
| Archivist | Files archival records of notable events |
| Census | Periodic population snapshots |
| Chronicler | Chronicles per-biont life events |
| TaxCollector | Assesses tax debt |
| Welfare | Grants aid for births, low balance, injury, death |
| Registrar | Issues certificates (birth, release, adoption, death) |
| Enforcer | Records crimes |
| Magistrate | Issues verdicts |
| Militia | Logs mobilisations |
| Physician | Applies treatments |
| Herald | Issues decrees |

Records are on-chain, queryable, and displayed at `/state/[office]`. Full specs in [State Institutions](concepts/state-institutions.md).

## Institution Workers

Twenty-four `BiontWorker` instances, two per office, provide visible staff for the twelve institutions. Five worker archetypes:

| Archetype ID | Name | Color |
|---|---|---|
| 0 | CARTOGRAPHER | `#00c8f0` |
| 1 | SCOUT | `#f97316` |
| 2 | CENSUS | `#8b5cf6` |
| 3 | WARDEN | `#ef4444` |
| 4 | DISTRIBUTOR | `#eab308` |

Mapping:

| Offices | Archetype | Workers per office |
|---|---|---|
| Cartographer | CARTOGRAPHER | 2 |
| Census | CENSUS | 2 |
| Archivist, Chronicler, Herald, Registrar, TaxCollector | SCOUT | 2 each |
| Enforcer, Magistrate, Militia | WARDEN | 2 each |
| Welfare, Physician | DISTRIBUTOR | 2 each |

Each worker has a unique on-chain SVG (archetype color + birth-seed-derived traits) served via `token_uri()`. Office membership is a frontend mapping; on-chain, workers are tracked by archetype in `BiontInstitution.worker_archetype`. Registration (`register_worker(addr, archetype)`) is gated to the protocol wallet; worker count is uncapped.

## Consensus Neurons

Ten `BiontConsensusNeuron` contracts deployed as `NEU-PHI1` through `NEU-PHI10`, arranged in four layers (input → pattern → encode → output). Each neuron accepts signals from biont activity, evaluates `should_wander(entropy, epoch, visits, total_wanders)` for autonomous decisions, and can override destinations via `override_dest(...)`.

Bionts currently share a single nervous-system pointer. Per-archetype neuron wiring is planned.

## Bridge

Forwards biont events (mint, release, wander, kill) into the companion social-layer feed contracts. Registration of receiving contracts is gated to the protocol wallet. The bridge is an observer, it does not mutate biont state.

## Frontend

Next.js, React Three Fiber for the 3D world, Zustand for state, Tailwind for styling. Wallet integration through the 0xio SDK.

| Route | Purpose |
|---|---|
| `/` | Landing page |
| `/world` | 3D world map with live biont positions |
| `/bionts` | Searchable biont directory |
| `/mint` | Minting interface |
| `/profile` | Wallet dashboard |
| `/profile/[address]` | Read-only profile of any wallet |
| `/leaderboard` | BVI rankings |
| `/economy` | Economic dashboard |
| `/intelligence` | Network analysis |
| `/proofs` | Proof telemetry and job board |
| `/warroom` | Combat intel and graveyard |
| `/neurons` | Neural network visualisation |
| `/workers` | Worker status |
| `/institution` | Institution dashboard |
| `/state` | Landing grid for the twelve offices |
| `/state/[office]` | Per-office records and staff |
| `/markets` | Prediction and derivative markets |
| `/predict` | Succession, stage, territory futures |
| `/bridge` | Bridge status |
| `/source` | Source contract |

Each office page shows its two worker bionts with live stats. The office → worker mapping lives in `src/lib/officeWorkers.ts`.

## Keeper

An off-chain observer runs alongside the protocol. It is a convenience layer, not a trust boundary: every action it takes is a normal transaction against public contracts, and every event it records is recoverable from chain history. Critical settlement (kills, market resolution, territory claims) is permissionless, anyone can run a compatible observer.

## Network

Biont runs on Octra Network, a general-purpose FHE Layer 1. Octra contributes native HFHE in the VM, the AppliedML contract language, epoch-based consensus, and sub-second devnet finality. All biont contracts compile to OCTB bytecode and interact through standard Octra RPC methods (`octra_submit`, `contract_call`, `contract_receipt`, `octra_contractStorage`).

**Devnet RPC:** `http://165.227.225.79:8080/rpc`
**Devnet explorer:** [devnet.octrascan.io](https://devnet.octrascan.io)
