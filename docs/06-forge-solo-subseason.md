# Forge Solo Subseason

## Purpose and boundary

`ForgeSoloSubseason` is a separate Paper gameplay plugin project, not part of Fern’s hosting interface. It is intended as a class/economy/progression experience for the private server. The README identifies a Paper/Minecraft 26.2 baseline and Java 25+. In the project history, the live server later reported Paper 26.3; the plugin’s actual runtime compatibility with that server must be verified rather than assumed.

The project is marked **test-only**. The static balance review explicitly says no gameplay changes were made and concludes the repository is not ready for public multiplayer launch as a complete skills/progression plugin.

## Current foundations described in source

The Paper entrypoint wires a local ledger, class repository, offline base-defense repository, fail-closed claims-service interface, anti-cramming task, telemetry, and commands/listeners for wallet, pay, class, skills, and baseguard. The repository is split into modules for areas including claims, classes, data, ledger, leveling, mob rewards, orbs, quests, routes, seasons, and skills, with a Paper platform module and a Velocity platform module.

Implemented or scaffolded behavior includes:

- **Wallet/economy:** local ledger operations, wallet display, player-to-player payment command, mob reward calculations and transfers.
- **Classes:** one-time class choice among Lumber, Miner, Craftsman, Marksman, and Healer; persisted selection through a local repository.
- **Offline base protection:** `/baseguard` flow with bounded radius; protection is intended to apply while the owner is offline.
- **Skills/catalog:** skill and subclass definitions plus mana/cooldown math and a runtime gate. The catalog is informational; actual Paper ability effects are not wired.
- **Mob rewards:** reward calculations, spawner provenance tracking, anti-cramming, reward caps/rate controls, and ledger mint calls.
- **Aggregate telemetry:** local UTC-day CSV counters, no usernames/UUIDs/IPs/chat/raw commands, and no external upload in the reviewed implementation.
- **Tests:** unit-style tests exist for helpers such as math, catalogs, clocks, claim service behavior, rewards, and telemetry storage.

These features are foundations and do not imply complete persistence, safety, or balance.

## Highest-priority review findings

### Claims authorization bypass — release blocker

The event handler applies a `claims.canModify(...)` check only inside a restricted-material branch. Allowed Lumber/Miner breaks can bypass it, and placement/general interaction paths do not invoke the needed claims authorization. The project initializes a fail-closed service, but the reviewed interface alone does not establish a complete authoritative provider. Claims must run before class/material exceptions for every state-changing action.

### Skills can consume resources without an effect — release blocker

`SkillRuntime.cast` checks cooldown and mana, spends mana, sets cooldown, and returns success. It does not dispatch an effect. No Paper cast route or complete skill/class event wiring was established. The `/skills` view is informational. A successful “cast” can therefore be a no-op. Either keep skills clearly preview-only or implement effects transactionally: validate, apply the action, then commit resource/cooldown state.

### Routes/seasons/economy/persistence

The balance review reports that deterministic caller-supplied route seeds can be searched, and uniqueness is only in memory, making rerolls possible after restart. The season clock does not implement the complete intended phase lifecycle. Reward caps, rate buckets, and spawner provenance are held in memory; reward state may advance before asynchronous ledger delivery succeeds. Ledger accounting needs a correct invariant and idempotent, failure-atomic transfers.

### Protection, cleanup, and input validation

The review notes incomplete piston destination checks, missing ignition coverage, persistence failure behavior that can diverge from in-memory state, and broad anti-cramming removal that can clear eligible mobs when a chunk reaches the count threshold even if they are dispersed. It also flags unbounded/non-finite values in mana/quest/loan paths and a mob reward table that may not match the written design.

## Balance and measurement limits

The review says some helper formulas align broadly with written formulas, but that is not evidence of balanced gameplay. Population, average playtime, mob-kill rate, time-to-level, server restart frequency, and desired economy inflation were not defined. Telemetry cannot currently measure actual skill usage or power because the effects are not wired. Collect measurable targets and validate the full production wiring before tuning values.

## Recommended stabilization order

1. Make claims authorization authoritative for every protected mutation; add Paper integration tests for owners, members, unclaimed land, and fail-closed behavior.
2. Make skills either explicitly preview-only or real. Add effect handlers and transactional resource/cooldown semantics.
3. Replace player-influenced route seeds with secure server-generated randomness and persist the result before revealing it.
4. Implement and test explicit season phases, restart recovery, and late-join behavior.
5. Persist reward caps, rate limits, and provenance; make ledger delivery idempotent and failure-atomic; fix balance invariants.
6. Complete protection edge cases, replace broad cleanup with local density-aware behavior, and harden numeric/spec validation.
7. Establish balance targets and run controlled tests using representative population and playtime data.
8. Test the plugin on the actual intended Paper version, including the reported 26.2/26.3 difference.

The review is a static code review and records recommended work—not completed fixes. See the [source review](https://github.com/kammonusinno-ops/forge-solo-subseason) for current repository content; the archived balance-review note is held separately from the main Fern repository.