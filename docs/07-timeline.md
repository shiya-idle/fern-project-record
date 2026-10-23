# Project timeline

This timeline follows the preserved conversation/task context. Dates are included only where the history has a clear timestamp; early events are listed in sequence, not assigned fabricated dates. Commands shown by the user were user-executed unless a saved source explicitly says otherwise.

## Initial planning — date not reliably retained

- User asked for a single Minecraft server for themselves and friends, initially considering free/low-cost cloud options and ReadySpace/Kassa Technology cPanel.
- The product goal became a custom minimalist green/white interface using Monaspace Radon for normal UI text and Monaspace Xenon for logs/terminal/code.
- User explicitly declined Manus WebDev for the site and requested a GitHub repository and a cPanel-importable package.
- User preferred a useful hosting/file-input panel over marketing copy and generic placeholder language.

## Infrastructure setup — historical sequence, exact date partly retained

- User chose AWS for the backend after exploring Oracle Cloud/other VM questions. The final target described in the project notes is Ubuntu 24.04 with Pterodactyl Panel and Wings; older stages included a separate EC2 environment and earlier image/OS experiments.
- The user manually handled AWS console, SSH/Termius, volume expansion, security rules, DNS, cPanel uploads, MariaDB/PHP setup, Pterodactyl installation, Wings configuration, allocations, and panel setup with step-by-step guidance.
- The preserved history contains troubleshooting around SSH connection, one-line commands, Panel setup fields, Wings auth/config, daemon port 8080 already bound by Wings, browser WebSocket origin rejection, Bedrock UDP exposure, and confusion about Pterodactyl node/allocation IDs.
- The final architecture separated ReadySpace/cPanel (custom interface) from AWS/Pterodactyl/Wings (server execution). Exact current infrastructure health is unknown from this archive.

## Fern product scope — continued

- User asked for a practical hosting feature set: server lifecycle controls, file/SFTP access, live console, logs/resources, Paper default, and Java/Bedrock support.
- User chose a guarded one-time server-provisioning flow through a Pterodactyl Application API key after removing a disposable test server and resolving node/allocation values.
- Automatic Geyser/Floodgate setup was requested so Bedrock crossplay could be provisioned alongside Paper.
- An owner-only console, file manager, resource panel, and separate public player experience were added to scope.

## Player portal and whitelist — October 2026 project sequence

- User asked for a non-owner area with no owner-panel password, player login/profile, Java/Bedrock whitelist form, public online/offline roster, future leaderboard, and updates.
- The selected authentication mode was passwordless player sign-in and immediate whitelist. This reduces friction but is not proof that the visitor owns the supplied Minecraft identity.
- A Bedrock player with client version 26.52 could not connect; the project investigated console output and Geyser/Floodgate behavior.
- User reported a XUID/Gamertag mismatch. The code path was examined and later corrected to send a Gamertag with its original spaces to Geyser’s verification endpoint rather than replacing spaces with underscores. Floodgate may use underscore-normalized in-game names, but that is a different interface contract. The change’s upload/production verification is not documented.

## Gameplay plugin and data — October 22 context

- User supplied the `forge-solo-subseason` repository and asked to adapt plugin targeting toward Minecraft 26.2 and review balance.
- A separate balance review was produced. It called out claims bypass and no-op skill casting as highest priority, plus route randomness, season lifecycle, reward persistence/economy, protection, validation, and missing target metrics. The review made no gameplay changes and keeps the plugin test-only.
- User asked for data collection to support a later balancing review. A separate telemetry path and SQL schema records session/event/replay metadata and exposes a player-hours chart.

## Replay prototype and pause — October 22–23 context

- User asked for 20-hour server-side replay capture, initially described as 144p third-person video; after discussion, the approved design became server-side Flashback replay files rather than MP4.
- User specified visible notice, chat excluded, admin-only access, player-hours chart, test Paper 26.3 compatibility first, a separate project and disposable server, and deployment pending AWS/ReadySpace endpoint configuration.
- The prototype locally creates Flashback-format segments, bounded retention, and a Fern delivery queue. Its checkpoint reports local Gradle/build/structural tests but no real playback or runtime compatibility verification.
- User explicitly said to pause and continue only when cued. The required cue is **“continue flashback.”**

## Recent Fern usability and configuration work — October 22 context

- User asked about Java/Bedrock chat because Bedrock players saw a chat restriction. The guidance distinguished server secure-chat configuration (`enforce-secure-profile=false` for Geyser compatibility while keeping `online-mode=true`) from Xbox/Microsoft privacy settings on the player account.
- User asked for a better file/code editor because editing was inconvenient. The Fern front end was upgraded locally from prompt-based text editing to a bundled CodeMirror 5.65.20 modal editor with syntax modes, line numbers, find/replace, indentation, dirty state, and keyboard save.
- A cPanel-ready ZIP was created in the previous task. The bridge and front-end changes were uncommitted in the local checkout when this archive was started; cPanel upload was not confirmed.
- User asked about `server.properties` and requested that the relevant options be captured. The Settings page currently edits the file as text; no structured form was proven deployed.

## Archive request — October 23, 2026

- User requested a detailed record of tools/skills used, how the project was built, and how it was planned, placed into a new repository.
- This archive was prepared from source, READMEs, review notes, pause checkpoint, and preserved task history. A fresh parallel source review compared interface, backend, infrastructure, metrics, replay, gameplay review, and history before writing this repository.

## Unknowns that should remain unknown

This task archive does not confirm the exact current AWS instance state, public address, DNS propagation, firewall/allocations, Pterodactyl health, cPanel contents, database availability, live Paper version, Bedrock connection success, editor ZIP upload, or deployment of the XUID fix. Do not convert old troubleshooting or example configuration into a claim of current production status.