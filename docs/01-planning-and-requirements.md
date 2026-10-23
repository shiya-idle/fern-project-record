# Planning and requirements

## Starting goal

The initial request was to host one Minecraft server for the user and friends, with a custom control website rather than a generic commercial-hosting panel. The visual brief was minimalist green and white, using Monaspace **Radon** for the main interface and Monaspace **Xenon** for logs and code. The user wanted practical server management, file inputs, and hosting controls—not decorative filler or “random words.” The custom interface was to run on ReadySpace/Kassa Technology cPanel, and the source was to live in GitHub. The user explicitly chose not to build the product in Manus WebDev.

The original “clone Witherhost” request was translated into a focused set of useful hosting functions rather than an attempt to reproduce an entire commercial product: start/stop/restart, console, file access, resource/status information, backups, server creation, Java/Bedrock support, and an accessible website.

## Scope decisions in order

1. **Split interface from runtime.** ReadySpace/cPanel became the home for Fern’s custom PHP/JavaScript UI and API bridge. AWS became the location for the Pterodactyl Panel/Wings backend and the actual Minecraft server. This respected the user’s preference for a custom frontend while using Pterodactyl to execute server operations.
2. **Use Pterodactyl rather than rebuilding a game panel.** The panel and Wings expose the established server lifecycle, file, console, backup, and WebSocket functions. Fern remains the small, single-server interface.
3. **Choose Paper as the default server software.** The user asked that Java servers default to Paper and approved a constrained one-time creation flow. Server creation is not intended to become a multi-tenant public hosting business.
4. **Add Bedrock crossplay through Geyser and Floodgate.** Geyser translates Bedrock connections to a Java server; Floodgate is the planned authentication bridge for Bedrock players who should not need a Java account. The design requires separate Java TCP and Bedrock UDP allocations.
5. **Keep player access separate from owner control.** The player site is public and has no owner-panel password. The owner dashboard still requires a password. The player portal provides Java/Bedrock whitelist entry, roster, planned leaderboard, and updates.
6. **Add measurable, privacy-limited telemetry.** The user wanted data that could later help assess gameplay balance. The design stores aggregate/session metadata rather than chat or personal raw histories. The owner dashboard shows hours played over a 20-hour rolling window.
7. **Explore a replay recorder as a separate project.** The user clarified that “third person/144p” video could not be produced by an ordinary server-side recorder and approved server-side replay files instead. The separate prototype uses Flashback-format replay files, not MP4. The user later explicitly paused this work.
8. **Keep the gameplay plugin separate.** Forge Solo Subseason is a separate Paper plugin project with a different product scope. It is not Fern’s hosting panel and is test-only until its review findings are resolved.

## Product requirements captured

### Fern hosting panel

- One owner-managed Paper server, with start/stop/restart, live console, file/config editing, backups, status, resource display, and a simple server address.
- A constrained create-once Paper path; no arbitrary egg selection, resource plans, deletion, or creation of extra servers through Fern.
- Java access on TCP 25565 and Bedrock crossplay on UDP 19132, subject to the Pterodactyl node’s allocations and AWS/network exposure.
- A custom cPanel interface that proxies server-side API requests to Pterodactyl and does not expose API keys to JavaScript.
- A calm green/white visual system, Radon interface type, Xenon terminal/code type, and mobile-friendly operation.

### Player portal

- No owner password required for ordinary players.
- Local player profile captures edition, Minecraft name/Gamertag, and platform identifier.
- Java uses a Java UUID; Bedrock uses a hexadecimal XUID checked against a Floodgate UUID result.
- Self-service whitelist submission, public online/offline/unknown roster, and future leaderboard/updates sections.
- The passwordless profile is convenience data, not proof of account ownership. Immediate whitelist acceptance was a user-approved product choice, but it carries abuse risk.

### Telemetry and replay

- HMAC-authenticated session-event intake.
- Player-hours visualization over a rolling 20-hour interval.
- Separate server-side replay files, visible notice, chat excluded, admin-only access, 20-hour retention, and a 5-GiB local cap in the prototype.
- Intended external-storage policy: use AWS first up to about 5 GB, then use ReadySpace. The storage endpoints and file-transfer workflow remain unconfigured; this is not an active capability.

### Forge Solo Subseason

- Separate Paper plugin foundation for wallet/economy, classes, skills, offline base protection, rewards, and aggregate telemetry.
- Target discussed as Minecraft/Paper 26.2; a later live-server status was reported as Paper 26.3, so compatibility must be demonstrated before relying on the plugin.
- The balance review made no gameplay changes. The findings remain open and the plugin remains test-only.

## Design and security rationale recorded from the project

The interface was kept narrow because this is one server for a known group, not a public host marketplace. Pterodactyl owns node/server execution, while the PHP bridge owns Fern-specific access control, validation, and translation between the browser and panel APIs. Keeping credentials server-side avoids sending Pterodactyl API keys to visitors. Player access is separate because players should not need the owner’s password. Crossplay is an explicit installation/setup path, not a vague toggle. Metrics are metadata-only because the user asked for review and balancing—not collection of chat transcripts or MP4 recordings.

The replay scope was deliberately revised from “record everyone in 3rd person at 144p” to “record server-side replay files.” A server cannot record a client’s rendered third-person camera as video; the approved alternative was replay data interpreted by compatible tooling. The replay branch was later paused by the user, and its checkpoint conditions remain binding.

## Non-goals and constraints

- Not a public multi-customer hosting reseller.
- Not a replacement for Pterodactyl’s node/runtime layer.
- Not a video/MP4 surveillance system.
- Not permission to deploy unreviewed gameplay code to the live world.
- Not evidence that an example config, README, build, or syntax check means the service is running in production.
- Not permission to publish private IPs, passwords, API tokens, HMAC keys, database credentials, or filled Fern configuration.

## Evidence

The main Fern requirements are reflected in the Fern README, UI source, PHP bridge, configuration example, and SQL schema. Replay requirements and pause conditions are reflected in the separate replay README and `PAUSED_PROGRESS.md`. The Forge scope and release posture are reflected in its README and the balance review. See [Source index](../appendix/source-index.md).