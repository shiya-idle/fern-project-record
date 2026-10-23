# Architecture

## System map

```text
Players / owner browser
    │ HTTPS: Fern website + same-origin PHP API
    ▼
ReadySpace / cPanel
    ├── index.html, app.js, styles.css, fonts, CodeMirror assets
    ├── api.php — sessions, validation, HMAC intake, proxy requests
    └── private config + MySQL/MariaDB metrics (outside public_html)
              │ HTTPS: Pterodactyl Client/Application APIs
              ▼
AWS: Pterodactyl Panel ───── Pterodactyl Wings ───── Docker/Paper server
          ▲                                            │
          └──────── browser WebSocket (wss, token) ────┘
                                                       ├── Java TCP 25565
                                                       └── Bedrock UDP 19132 via Geyser
```

The diagram represents the intended topology in code and README. It does **not** establish that these services are currently reachable or correctly deployed.

## Responsibility boundaries

### Fern on ReadySpace/cPanel

The cPanel host serves the static site assets and PHP bridge. `app.js` never needs to hold a Pterodactyl API key. The browser sends same-origin requests to `api.php`; the bridge adds private credentials when it proxies a Pterodactyl call. The PHP config is expected above `public_html`, not downloaded or committed. Static HTML/CSS/JavaScript makes the UI simple to upload and maintain through cPanel.

### Pterodactyl Panel and Wings on AWS

Pterodactyl is the control-plane API. Wings is the node agent that manages Docker containers, file access, and server lifecycle. Fern does not recreate Wings or own Docker. It requests actions from Pterodactyl and renders the responses. One Minecraft server is the product scope.

The README documents Panel URL `panel.forgeholdings.name`. The desired game allocations are Java TCP 25565 and Bedrock UDP 19132. Wings’ WebSocket endpoint must be reachable by the owner’s browser over secure WebSocket (`wss`) for the console to work. The exact current IP, certificate, firewall, security group, and allocation status are not included here.

### Minecraft and crossplay

Paper is the default server software. Geyser handles Bedrock-to-Java protocol translation, and Floodgate supplies the identity/authentication integration chosen for Bedrock players. Java and Bedrock are different listeners/protocols: the Java allocation is TCP; Geyser’s Bedrock listener is UDP. Having both allocations in Pterodactyl does not by itself prove AWS firewall rules or plugin configuration are correct.

### Metrics database versus replay files

MySQL/MariaDB stores session rows, event rows, replay metadata, a recording key, size, declared storage tier, and timestamps. It does not store raw replay archives or chat. The replay plugin’s local filesystem is a distinct storage plane. Planned AWS-to-ReadySpace overflow requires a real upload/migration service and configured endpoints; a database label such as `aws` or `readyspace` is not proof that an object exists there.

### Separate plugin projects

`ForgeSoloSubseason` is a gameplay/economy plugin project, not a component required to operate Fern. `fern-replay-capture` is a separate replay prototype intended for a disposable Paper test server. The replay project has its own build, plugin metadata, configuration, tests, pause checkpoint, and deployment boundary.

## Request and data flows

### Owner API flow

1. Browser loads Fern and calls the `me` action.
2. Owner login establishes a PHP session and CSRF token.
3. Non-GET owner actions add the CSRF header.
4. `api.php` checks the session, validates request input, and proxies through a Pterodactyl Client API key or (for guarded provisioning only) Application API key.
5. API keys stay on cPanel. The browser receives data/actions, not the keys.

The console is the exception in network route, not credential ownership: the PHP bridge requests a short-lived Pterodactyl WebSocket token and a `wss` socket URL. The browser then connects to Wings directly using that token.

### Public player flow

The public portal creates a separate player CSRF value. A player submits edition, name/Gamertag, and Java UUID or Bedrock XUID. The server-side bridge validates the identity before sending a whitelist command. Roster data combines `whitelist.json` with a Java status ping where the cPanel host can reach the game TCP port. If it cannot, status can be `Unknown` rather than falsely marking players online/offline.

### Metrics flow

The plugin sends event batches to `session-events` using an HMAC signature over timestamp and raw request body. The PHP bridge validates the HMAC/timestamp and schema, then transactionally upserts sessions, deduplicates events, inserts replay metadata, and prunes old metric rows. The owner-only `player-hours` action calculates time intersecting the 20-hour window. The browser draws a donut chart.

### Replay file flow

The replay plugin writes `.flashback` files locally, rotates them hourly or at a size bound, and applies local retention/cap rules. Fern’s metrics API may store metadata/file keys. External storage transfer is disabled/unconfigured in the checked sources and remains paused. The complete object-transfer, quota, fallback, and deletion pipeline is not implemented evidence.

## Component map

| Component | Primary responsibilities | Evidence/status |
|---|---|---|
| `index.html` | Page shells, navigation, local CodeMirror assets. | Source present. |
| `app.js` | Owner/public state, UI rendering, API client, console/editor interactions. | Source present. |
| `styles.css` | Radon/Xenon, green/white styles, responsive layout, dark console/editor. | Source present. |
| `api.php` | Sessions, CSRF, Pterodactyl proxy, provisioning, crossplay setup, file/backup calls, whitelist, telemetry. | Source present; deploy state unverified. |
| Private Fern config | Credentials, server identifiers, DB and HMAC settings. | Not included; never commit. |
| `fern_metrics.sql` | Session/event/replay-metadata tables and indexes. | Source present; application to live DB unverified. |
| `ForgeSoloSubseason` | Game mechanics, classes, ledger, claims/base defense, skills/rewards, aggregate telemetry. | Separate project; review says test-only. |
| `fern-replay-capture` | Packet capture, Flashback writer, local retention, privacy notice, Fern queue prototype. | Separate project; explicitly paused. |

## Operational assumptions that must be verified

- cPanel supports required PHP version/extensions, cURL, PDO MySQL, upload sizes, TLS, and outbound HTTPS.
- The private config is outside the public document root and filesystem-readable only to the web service owner.
- Pterodactyl Panel and Wings have valid HTTPS/TLS and the API keys have only required permissions.
- Wings WebSocket port or HTTPS proxy is reachable from owner browsers and accepts the correct website origin.
- Node allocations include TCP 25565 and UDP 19132 and matching AWS/network firewall exposure.
- Paper egg and Java image are compatible with the target server version.
- Geyser/Floodgate configuration, username prefix, UDP listener, and Bedrock account rules match the portal.
- MySQL schema, DSN, HMAC secret, server clock, and network path are available at runtime.
- Replay archive playback, plugin compatibility, and any storage transfer are tested before deployment.