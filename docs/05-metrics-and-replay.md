# Metrics and replay capture

## The product request

The user initially asked for a server-side system that could record every player in a third-person, 144p video for the most recent 20 hours, automatically discarding the oldest hour and using AWS storage first up to about 5 GB before ReadySpace. The product requirements were later made concrete: use **server-side replay files**, not MP4 video; 20-hour rolling retention; a visible notice; no chat; admin-only access; an owner-only player-hours chart; test Paper 26.3 compatibility first; make it a separate plugin; use a disposable test server; keep AWS/ReadySpace deployment pending endpoint configuration.

A server does not render the clients’ third-person camera view. The approved design records packet/replay data that a compatible client-side Flashback tool may render. It is therefore not a 144p video recorder and cannot guarantee an exact camera perspective like a screen recording.

## Fern session metrics

### Database tables

`sql/fern_metrics.sql` defines three tables:

- `fern_player_sessions`: one session row with session ID, player UUID/name, edition, start/end times, and indexes for player/time queries.
- `fern_session_events`: append-style event log for `session_started`, `session_ended`, and `replay_saved`; event ID is the idempotency key.
- `fern_replay_metadata`: recording ID, session ID, file key, size, declared storage tier (`aws`, `readyspace`, or `local`), and save time.

The schema stores replay **metadata**, not raw replay blobs, screenshots, chat, or videos. Storage-tier strings do not create the associated bucket or transfer data.

### Event intake and dashboard

The PHP bridge’s `session-events` route requires a JSON POST, validates a bounded batch and fields, validates a timestamp header and HMAC-SHA256 signature over the timestamp and raw body, and writes a transaction with duplicate event IDs ignored. Session starts/ends are upserted, and replay metadata is inserted by unique recording ID. The code prunes metric rows after the rolling 20-hour horizon during successful ingestion.

The owner-only `player-hours` route calculates the portion of each session overlapping the 20-hour window. Open sessions are clipped to now; sessions starting before the window are clipped to the window start. The UI renders a donut with player totals, a 20-hour label, and loading/error/no-data states. This chart summarizes playtime; it is not a replay index.

The retention implementation visible in the bridge applies to database rows. No physical replay-file deletion, object migration, quota enforcement, or background reconciliation was established by the reviewed metrics files.

## Replay plugin prototype

The standalone `fern-replay-capture` project has a Paper plugin entrypoint and separate components for packet capture, replay writing, session/segment management, local storage, privacy rules, and a Fern retry queue. It is separate from `ForgeSoloSubseason` and from the Fern website.

Source/configuration describes:

- Recording starts at player join and closes at player quit.
- Files are `.flashback` ZIP archives containing `metadata.json` and `c0.flashback` action data.
- Segments rotate hourly or at the configured size bound.
- Local retention is limited to 20 hours and 5 GiB total.
- A visible notice explains recording and chat exclusion; replay administration is gated by `fernreplay.admin`, defaulting to operators.
- Chat-like packets are deliberately excluded; there is no MP4 capture.
- Fern event delivery and external storage are disabled/configurable until endpoint setup.

The checkpoint reported local Gradle build/test success, JAR artifacts, and validation of ZIP structure/retention/event-signature behavior. This is not proof of Flashback-player compatibility. No real client playback or end-to-end Paper 26.3 runtime smoke test was completed. Do not install the JAR on the live server.

## Storage plan versus implementation

The user’s desired policy is AWS first, capped around 5 GB, then ReadySpace overflow. In the checked source, local disk retention and the metadata enum exist, but no implemented AWS upload, 5-GB accounting, ReadySpace endpoint, overflow routing, object replication, migration, or physical deletion was evidenced. The README says external transfer is pending endpoint, bucket, and credentials. Do not state that raw recordings are being saved in both locations.

## Pause boundary

The user said to pause Flashback work and resume only when they say **“continue flashback.”** Until then:

- Do not continue implementation or testing that starts a server.
- Do not accept a Minecraft EULA.
- Do not install the replay JAR on the live server.
- Do not deploy, commit, or push replay plugin code.
- Do not configure AWS/ReadySpace endpoints or credentials.

This documentation records the prototype; it does not resume the prototype.

## Eight recorded blockers before staged testing

1. **Main-thread network work:** `FernQueue.enqueue()` is reported to perform synchronous retry HTTP inline. Join/quit hooks must not block the Paper main thread. Move requests to an asynchronous worker/scheduler.
2. **Delayed event timestamps:** strict ±5-minute event timestamp validation conflicts with queued retries. Keep request-signature freshness protection while allowing delayed event-time semantics bounded by the replay retention policy.
3. **Floodgate UUID validation:** strict UUID version/variant checks can reject legitimate Floodgate/XUID-derived identities. Separate canonical UUID shape checks for player IDs from stricter event/session ID rules.
4. **Netty channel lookup:** reflection is broad/recursive and may be brittle across Paper versions. Test it against disposable Paper 26.3 and fail safely.
5. **Capture timing and playback sufficiency:** installing capture after join may miss initial state; an empty initial snapshot and raw PLAY packet stream may be insufficient. Verify real client playback and packet requirements.
6. **Flashback compatibility:** the custom structural validator does not prove the archive is accepted by Flashback. Open output with a compatible Flashback client before claiming compatibility.
7. **Notice and command contract:** the join notice should explicitly state the 20-hour retention period. `plugin.yml` advertises `list`, while the current handler supports `status`, `retry`, and `notice`; align descriptor and implementation.
8. **External service/runtime setup:** AWS/ReadySpace endpoints, bucket, upload route, credentials, and Fern MySQL runtime were not configured/verified. The checkpoint also noted the PDO MySQL driver was unavailable in its environment.

These items come from `PAUSED_PROGRESS.md`; some integration-specific details are checkpoint-reported and were not independently re-run during archive creation.

## Resume sequence (only after user cue)

1. Re-read the pause checkpoint and project constraints.
2. Fix queue threading, event timestamp policy, UUID validation, capture discovery/timing, command metadata, and notice text.
3. Use only a disposable Paper 26.3 test server.
4. Verify join/quit, segments, retention, permission checks, chat exclusion, error/retry behavior, and HMAC contract.
5. Open real output in a compatible Flashback client.
6. Only after user approval and endpoint decisions, configure external storage and test object lifecycle.
7. Keep all credentials private and never change the live server without explicit approval.