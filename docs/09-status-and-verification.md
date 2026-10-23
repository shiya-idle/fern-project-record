# Status and verification matrix

The terms below are used consistently: **Implemented** = source behavior exists; **Deployment-dependent** = requires live config/services; **Planned** = desired but not evidenced in source; **Paused** = explicitly halted; **Unverified** = no runtime/deployment evidence; **Finding/blocker** = known defect or release risk.

| Area | Status | Evidence available | What is not proven / next verification |
|---|---|---|---|
| Fern green/white responsive UI, Radon/Xenon fonts | Implemented in local source | `index.html`, `app.js`, `styles.css`, local font assets. | Production upload and browser rendering on cPanel. |
| Owner login, sessions, CSRF | Implemented in PHP/UI source | `api.php`, UI login flow, example config. | Live session/cookie/HTTPS behavior and security review. |
| Pterodactyl status/power/files/console/backups | Implemented as API/UI paths | Fern source and action contracts. | Real Panel/Wings authorization, file/backup operations and browser `wss` console. |
| One-time Paper provisioning | Implemented but disabled by default | Guarded config/API code with fixed IDs and limits. | Actual credentials, owner/node/egg/allocations, Java image, and create-once operation. |
| Geyser/Floodgate auto-install | Implemented as guarded path | Artifact metadata/hash/upload logic. | Both plugins installed, correct prefix/auth, UDP 19132, and Bedrock join test. |
| Java whitelist verification | Implemented in source | Mojang lookup and server command path. | Live portal test with correct Java name/UUID. |
| Bedrock XUID/Gamertag verification | Implemented with recent name-lookup fix in local source | Geyser lookup + XUID suffix matching; name now passed unchanged. | Upload to cPanel and successful portal retry; Floodgate prefix must match. |
| Public roster | Implemented as UI/API flow | Whitelist-file/status logic and unknown-state UI. | Live status/roster response and cPanel outbound game-port access. |
| Leaderboard and Updates | Planned/empty | Explicit placeholder UI. | Data source, permission, and product behavior. |
| Code editor | Implemented locally | Bundled CodeMirror 5.65.20, mode mapping, dirty/save logic, local assets. | Upload matching frontend and PHP bridge; test every mode, large files, save failures, mobile keyboard and permissions. |
| `server.properties` UI | Implemented as text editor | Settings page reads/writes file through API. | Production setting values and restart behavior. No structured form proven. |
| Java/Bedrock chat compatibility | Guidance identified | Geyser secure-chat and Minecraft account docs. | Set `enforce-secure-profile=false` if needed while keeping `online-mode=true`; each player account may need Xbox privacy changes. Configuration not verified. |
| Session metrics/HMAC | Implemented in source | `api.php`, config example, SQL schema. | PDO MySQL driver, live DB/schema, secret, client event timing and ingestion. |
| Player-hours chart | Implemented in source | Owner-only SQL window query and SVG donut. | Live data and deployed UI validation. |
| Rolling metrics retention | Implemented during ingestion | Pruning transaction in PHP. | Physical replay files are not shown being deleted or migrated. |
| Replay capture plugin | Prototype built and locally validated per checkpoint | Replay source, config, tests, local JARs and pause notes. | Runtime capture, channel lookup, Paper 26.3 compatibility, actual playback and production security. |
| Replay main-thread HTTP / delayed timestamps / Floodgate UUIDs | Finding/blocker | Eight-item `PAUSED_PROGRESS.md`. | Resolve before staged testing, after user says `continue flashback`. |
| Replay storage AWS→ReadySpace | Planned and paused | User requirement, config stub, tier metadata. | No quota, upload, failover, migration, replication, endpoint or object-deletion implementation verified. |
| Forge Solo Subseason gameplay plugin | Test-only | Multi-module source, README, static review. | Claims and skill blockers, persistent randomness/economy, full Paper integration, balance targets, 26.2/26.3 runtime testing. |
| Current AWS/cPanel/Pterodactyl health | Unverified | Historical setup conversation and README. | Live service/device inspection and owner confirmation; no current production status claimed. |

## Release gates

### Fern site

- Confirm latest ZIP/source has been uploaded to the correct cPanel path and private config remains outside the public root.
- Check HTTPS, PHP runtime/extensions, MySQL driver/schema, error logging, upload limits, and outbound connectivity.
- Exercise owner authentication/CSRF, status, console, power, files/editor, settings, backup, provisioning (if intentionally enabled), crossplay, whitelist, roster, and metrics.
- Review the self-service whitelist threat model and API-key scopes.

### Replay capture

- Wait for “continue flashback”; preserve the pause meanwhile.
- Resolve all eight blockers, use a disposable test server, prove actual Flashback playback, and verify permissions/notice/chat exclusion/retention.
- Configure external storage only after endpoints, credentials, quota/fallback policy, and file deletion/migration are designed and approved.

### Gameplay plugin

- Resolve claims and no-op skill casting first; then route/season/persistence/economy/protection/validation findings.
- Test the actual intended Paper version, not just helper methods.
- Establish population/playtime/mob-kill/leveling/economy targets and test with real telemetry before declaring balance.

## Verification discipline

A syntax pass proves parseability, not correct behavior. Unit tests prove only the exercised conditions. A successful API request in a local mock or static source inspection does not prove live DNS, firewall, TLS, Pterodactyl permissions, Java runtime, client compatibility, or user-facing correctness. Update this matrix only with new source, test, or deployment evidence and record the date and environment of each check.