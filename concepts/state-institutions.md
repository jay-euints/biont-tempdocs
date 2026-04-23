---
sidebar_position: 4
title: State Institutions
---

# State Institutions

Biont Network has a state layer: twelve observation contracts that record, tax, certify, and adjudicate citizen bionts. Every mint, release, kill, hardening change, and epoch milestone produces records in this layer.

There is no centralised authority. Each institution is a separate contract with its own storage. Anyone can read any institution's records.

## The Twelve Offices

| Office | What it records |
|--------|-----------------|
| **Cartographer** | Destination-to-destination edges (topology of the wandering graph) |
| **Archivist** | Notable events: births, kills, deaths, adoptions, successions, forges, territory claims, conversions |
| **Census** | Periodic population snapshots (alive, dead, held, released) + archetype tallies |
| **Chronicler** | Per-biont life event timeline (birth, release, wander, hunt, kill, die, adopt, evolve, forge, tribute) |
| **TaxCollector** | Tax debt assessments on wander, kill, convergence, release, forge, tribute, penalty |
| **Welfare** | Aid grants: low-balance, injured, orphaned, death-benefit, protocol-grant, relief, pension |
| **Registrar** | Certificates: birth, name-change, evolve, release, transfer, adoption, death, forge, marriage, decree |
| **Enforcer** | Crime records: peaceful kill, blacklist trigger, hunt failure, bounty violation, repeat offense, tax evasion, territory violation, decree defiance |
| **Magistrate** | Verdicts on crimes: dismissed, reprimand, fine, blacklist, banish, death-seal, pardon |
| **Militia** | Mobilization log: patrol, mobilize, repel, protect, escort, stand-down, drill |
| **Physician** | Treatments: diagnosis, harden-boost, revive, quarantine, relief, inoculate, autopsy |
| **Herald** | Decrees: general, tax, aid, law, verdict, mobilize, festival, emergency, pardon, obituary |

## How Institutions Populate

Every institution receives records as biont lifecycle events occur, births, releases, wanders, kills, hardening changes, and periodic civic activity. The effect is visible in the frontend: `/state/registrar` shows live certificates, `/state/militia` shows active patrols, `/state/archivist` shows a growing ledger.

## Reading the State Layer

The state pages on the frontend:

- **`/state`**, landing page with all 12 offices as deterministic SVG faces. Click any face to see its records.
- **`/state/[office]`**, per-office detail page. Shows:
  - The office's visual face (hash-derived from its contract address)
  - Office description and role
  - Live list of recorded events (edges, records, entries, decrees, etc.)
 - **STAFF panel**, 2 worker bionts with stats (archetype, active status, patrol count, report count, performance score)

All faces are deterministic: `hash(contract_address) → birth_seed → SVG`. Same address always produces the same face.

## Contract Interfaces

Each institution exposes a small, focused API. Below is a reference. All functions are owner-gated except where noted.

### Cartographer

```aml
fn record_edge(from: address, to: address): bool
view fn edge_count(): int
view fn edge_from(i: int): address
view fn edge_to(i: int): address
view fn edge_visits(i: int): int
```

### Archivist

```aml
fn file_record(kind: int, subject: address, counterpart: address,
               dest: address, weight: int, memo: string): bool
view fn record_count(): int
view fn record_kind(i: int): int
view fn record_subject(i: int): address
view fn record_counterpart(i: int): address
view fn record_weight(i: int): int
view fn record_memo(i: int): string
view fn record_epoch(i: int): int
```

### Census

```aml
fn file_snapshot(alive, dead, held, released, wanders, hardening, balance: int): bool
fn record_archetype(snap_id: int, archetype: int, alive_count: int): bool
view fn snap_count(): int
```

### Chronicler

```aml
fn file_entry(biont: address, ev_type: int, peer: address,
              dest: address, metric: int, note: string): bool
fn register_birth(biont: address, mint_epoch: int): bool
fn register_death(biont: address, death_epoch: int): bool
view fn total_entries(): int
```

### TaxCollector

```aml
fn assess_debt(kind: int, biont: address, amount: int, memo: string): bool
view fn total_assessed(): int
```

### Welfare

```aml
fn grant_aid(reason: int, recipient: address, amount: int, memo: string): bool
view fn aid_count(): int
```

### Registrar

```aml
fn issue_cert(kind: int, biont: address, counterpart: address,
              value: string, memo: string): bool
view fn cert_count(): int
```

### Enforcer

```aml
fn record_crime(kind: int, offender: address, victim: address,
                severity: int, memo: string): bool
view fn crime_count(): int
```

### Magistrate

```aml
fn issue_verdict(kind: int, defendant: address, plaintiff: address,
                 crime_ref: int, fine: int, rationale: string): bool
view fn verdict_count(): int
```

### Militia

```aml
fn log_mobilization(action: int, target: address, threat: address,
                    severity: int, notes: string): bool
view fn total_logs(): int  (or per-mobilization viewers)
```

### Physician

```aml
fn apply_treatment(kind: int, patient: address, severity: int,
                   outcome: int, notes: string): bool
view fn total_treatments(): int
```

### Herald

```aml
fn issue_decree(kind: int, subject: address, expires_epoch: int,
                headline: string, body: string): bool
view fn decree_count(): int
```

## Kind Constants

Referenced by hooks across multiple institutions:

### KIND (Archivist records)
`BIRTH=1, DEATH=2, KILL=3, ADOPTION=4, SUCCESSION=5, ALLIANCE=6, BREAKUP=7, CONVERGENCE=8, TERRITORY=9, FORGE=10, EXTINCTION=11, FIRST_VISIT=12, DECREE=13`

### EV (Chronicler events)
`MINTED=1, RELEASED=2, WANDERED=3, HUNTED=4, KILLED=5, DIED=6, ADOPTED=7, CONVERGED=8, EVOLVED=9, FORGE=10, TRIBUTE=11, TERRITORY=12, SUCCESSION=13, SOLD=14, DECREE=15`

### TAX (TaxCollector)
`WANDER=1, KILL=2, CONVERGENCE=3, RELEASE=4, FORGE=5, TRIBUTE=6, PENALTY=7`

### AID (Welfare)
`LOW_BALANCE=1, INJURED=2, ORPHANED=3, DEATH_BENEFIT=4, DEV_GRANT=5, RELIEF=6, PENSION=7`

### CERT (Registrar)
`BIRTH=1, NAME_CHANGE=2, EVOLVE=3, RELEASE=4, TRANSFER_TO=5, ADOPTION=6, DEATH=7, FORGE=8, MARRIAGE=9, DECREE=10`

### CRIME (Enforcer)
`KILLED_PEACEFUL=1, BLACKLIST_TRIG=2, HUNT_FAILURE=3, BOUNTY_VIOLATE=4, REPEAT_OFFENSE=5, TAX_EVASION=6, TERRITORY_VIO=7, DECREE_DEFY=8`

### VERDICT (Magistrate)
`DISMISSED=1, REPRIMAND=2, FINE=3, BLACKLIST=4, BANISH=5, DEATH_SEAL=6, PARDON=7`

### MOB (Militia)
`PATROL=1, MOBILIZE=2, REPEL=3, PROTECT=4, ESCORT=5, STAND_DN=6, DRILL=7`

### TX (Physician treatment types)
`DIAGNOSIS=1, HARDEN_BOOST=2, REVIVE=3, QUARANTINE=4, RELIEF=5, INOCULATE=6, AUTOPSY=7`

### DECREE (Herald)
`GENERAL=1, TAX_DEC=2, AID_DEC=3, LAW=4, VERDICT=5, MOBILIZE=6, FESTIVAL=7, EMERGENCY=8, PARDON=9, OBITUARY=10`

## Institution Identity

Each institution is deployed with its own contract address. The frontend renders each office's face deterministically from that address: `hash(contract_address) → birth_seed → SVG`. The face is a visual signature, two different addresses produce two visually different offices. Same address always renders the same face.

Faces are defined in `src/lib/institutionFaceGenerators.ts` with per-role themes, palettes, and shape rules. Rendering happens at `<InstitutionFace role="archivist" address={contractAddress} size={220} />`.

## The Bridge

A bridge contract sits adjacent to the state layer. It forwards significant biont events into a companion social-layer feed. The bridge is cross-protocol infrastructure, not a state institution itself.
