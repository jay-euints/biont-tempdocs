---
sidebar_position: 3
title: Institution Workers
---

# Institution Workers

The network has a second class of biont: worker bionts, deployed and managed by the `BiontInstitution` contract. Unlike citizen bionts (sovereign agents owned by users) workers are infrastructure agents that perform observation, recording, and subsidy tasks for the twelve state institutions.

## The five worker archetypes

`BiontWorker` supports five archetypes. These are the only roles a worker can hold:

| ID | Archetype | Colour | Role |
|---|---|---|---|
| 0 | CARTOGRAPHER | `#00c8f0` cyan | Maps destinations, records topology |
| 1 | SCOUT | `#f97316` orange | Patrols routes, files reports |
| 2 | CENSUS | `#8b5cf6` purple | Counts population, records snapshots |
| 3 | WARDEN | `#ef4444` red | Guards against threats, responds to incidents |
| 4 | DISTRIBUTOR | `#eab308` yellow | Distributes aid and treatments from institution treasury |

## How workers operate

Every worker is its own `BiontWorker` contract. Fields:

- `archetype`, immutable, set in constructor
- `institution`, address of `BiontInstitution`, sealed once via `set_institution(addr)`
- `my_addr`, the worker's own address, self-set once
- Dest registry and convergence pointers, wired to protocol infrastructure
- `birth_seed`, drives the on-chain SVG
- `is_active`, `incident_count`, `disruption_until`, `patrol_count`, `report_count`

**Actions:**

| Action | Caller | Effect |
|---|---|---|
| `patrol(dest)` | Anyone | Records a visit to a destination; increments patrol count |
| `execute_mission(action, target, data, amount)` | Protocol wallet | Performs an action (map, open, census, alert, distribute) after a permission check |
| `record_incident(attacker)` | Convergence / protocol wallet | Logs an attack; after three incidents the worker auto-deactivates |
| `set_guard_mode(active)` | Protocol wallet (wardens only) | Auto-files alerts on incidents |
| `receive_funds()` | Payable | Accepts OCT into the worker's operating balance |

**Permissions grid.** `BiontInstitution` maintains `action_permitted[archetype][action_type]`, with action types: `WANDER` (0), `MAP` (1), `OPEN` (2), `CENSUS` (3), `ALERT` (4), `DISTRIBUTE` (5). The institution's protocol wallet toggles grants via `set_action_permitted(archetype, action, 1)`.

## Worker identity

Workers generate their own SVG through `token_uri()`. The visual varies by archetype and seed: archetype-specific body shape (rectangle for CARTOGRAPHER, chevron for SCOUT, pentagon for WARDEN, and so on), a seed-driven eye variant, a frame with seal and title label, and an archetype-locked palette.

Workers appear on the world map and are part of the network's visible social fabric.

## Mapping twelve offices onto five archetypes

Twenty-four workers are registered, two per office across the twelve state institutions. Because `BiontWorker` only supports five archetypes, several offices share one:

| Office | Archetype |
|---|---|
| Cartographer | CARTOGRAPHER |
| Census | CENSUS |
| Archivist, Chronicler, Herald, Registrar, TaxCollector | SCOUT |
| Enforcer, Magistrate, Militia | WARDEN |
| Welfare, Physician | DISTRIBUTOR |

On-chain, workers are tracked in `BiontInstitution.worker_list[]` and `BiontInstitution.worker_archetype[addr]`. The office assignment (Archivist vs Chronicler vs Herald) is a frontend mapping in `src/lib/officeWorkers.ts`; on-chain, a SCOUT is just a SCOUT. This is the trade-off of using a five-archetype contract for a twelve-office layer, five distinct visual looks with twelve offices of semantic organisation on top.

## Funding

The institution treasury is fed by 25% of every mint.

```
mint (10 OCT)
  └─> 25% (2.5 OCT) → BiontInstitution treasury
                      ├─> fund_worker(addr, amount)
                      └─> fund_all_active_workers(amount_each)
```

Workers use their balance to pay operational costs (proof submissions, signal fees) and, for distributors, to carry OCT for distribution missions. Funding is discretionary, the institution's protocol wallet decides when and how much.

## Why workers matter

Workers are the visible manifestation of the state institutions. Each `/state/[office]` page displays its two workers as STAFF panels with archetype, active/disrupted status, patrol count, report count, and performance score. A thriving office has active high-patrol workers; a stagnant office has workers with no activity, a signal that the institution isn't being invoked.

## Running your own worker

Registration is gated: only the institution's protocol wallet can register new workers via `register_worker`. Independent workers deployed by users are not currently supported by the institution contract. Nothing stops you from deploying your own `BiontWorker` and running it against your own institution, but it won't appear on the main network's office pages. Permissionless worker registration with reputation-gated action permissions is open design space.
