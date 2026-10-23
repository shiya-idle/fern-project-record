# Fern interface

## Visual system

Fern’s interface is deliberately quiet and utility-first. The core palette is white and pale green surfaces with darker green actions/status, muted gray text, and a dark console/editor workspace. The primary text face is Monaspace **Radon**; logs and code use Monaspace **Xenon**. The HTML/CSS bundle includes local WOFF2 files and the `styles.css` rules. Layout uses compact cards, simple headings, short labels, restrained icons, and responsive breakpoints. The player portal and owner controls are separate page surfaces.

The design constraint came from the user: focus on entering files and hosting the Minecraft server, with no generic marketing filler. The application is a control surface for one server, not a multi-tenant hosting storefront.

## Owner dashboard

The owner login is separate from the public player site. After authentication, the owner navigation exposes:

- **Overview**: server status, player count placeholder/status display, memory and CPU figures, power controls, quick links, server address, and player-hours chart.
- **Files**: file/folder browsing, upload, folder creation, download, and editor access for common text/config/code files.
- **Console**: live output over Pterodactyl’s secure WebSocket token flow and command submission through the bridge.
- **Settings**: reads and writes the actual `server.properties` file; changes may require restart.
- **Backups**: list/create/download operations proxied to Pterodactyl.
- **Create server**: shown conditionally when the bridge reports that no server exists and provisioning is enabled; the form accepts a name and describes fixed Paper/port settings.

The controls are implemented as front-end calls, but their success depends on API permissions, backend configuration, service reachability, and a running server. UI presence alone is not proof of successful operation.

## Public Player Portal

The public portal has navigation for **Player sign in**, **Whitelist**, **Players**, **Leaderboard**, and **Updates**. Player sign-in asks for edition, Minecraft name/Gamertag, and platform identity. The browser stores this profile in localStorage to make the portal easier to revisit. This is passwordless convenience—not Microsoft/Mojang authentication and not proof of identity.

The whitelist page supports Java UUID and Bedrock hexadecimal XUID. It explains the two different ID formats and submits the identity to the PHP bridge, which should perform the authoritative check. The player list shows online/offline/unknown state and edition where the backend can supply it. Leaderboard remains a placeholder (“Coming soon”); Updates remains an empty announcement state.

The portal includes a notice that replay files/metrics have a 20-hour retention window and that chat is not captured. The separate replay checkpoint says the notice still needs to mention the retention period more explicitly in the plugin join notice; do not assume that all in-game notice versions have been updated.

## File manager and code editor

The original file-edit interaction used a browser `prompt()` with a large text field. The user found that inconvenient. The latest Fern source replaces that prompt with an in-page CodeMirror editor bundled locally in `assets/codemirror/`.

Editor behavior in source:

- Opens recognized text/config/code files in a modal that fills the screen on mobile.
- Shows the file name and server path, extension/UTF-8 label, save state, cursor line/column, and Close/Save controls.
- Includes line numbers, syntax modes for common files, bracket matching, automatic bracket closure, two-space indentation, Tab indentation, no line wrapping, find, and replace.
- Supports Ctrl/Command+S to save; the Save button is disabled until content differs from the original.
- Shows an unsaved state and asks before discarding changes.
- Saves through the authenticated `write` API action. The PHP bridge enforces the allowed extension set and 1 MiB text limit.
- Includes a plain textarea fallback if CodeMirror is unavailable; fallback does not provide the full syntax/search experience.

The bundled CodeMirror package was fetched locally as version 5.65.20 for the recent editor update. It is not loaded from a third-party CDN. The package’s license is stored with the bundled assets.

Current delivery status: the editor update and the Bedrock-space normalization fix were built and packaged locally. The user was told the refreshed ZIP had **not yet been uploaded to cPanel**. Treat cPanel deployment as unverified until the owner confirms it.

## Files and fonts

The original Fern source repository is `shiya-idle/craft-control-panel`. Relevant paths include `index.html`, `app.js`, `styles.css`, `assets/fonts/`, `assets/codemirror/`, `api.php`, `config.example.php`, and `sql/fern_metrics.sql`. The README and font-license file document the distribution context. Do not publish a filled private config or copy secrets into the archive.

## Current UI gaps

- The player login profile is browser-local and can be edited or cleared by the visitor; backend validation must not trust the local `whitelisted` flag.
- Immediate public self-whitelisting has abuse risk. If only invited friends should join, a trusted invite/moderation flow is still a product decision.
- Leaderboard and Updates are empty/planned; no real ranking or CMS behavior is evidenced.
- Public roster accuracy depends on Pterodactyl `whitelist.json` and a successful TCP status ping from cPanel.
- Console can fail if Wings TLS, allowed origins, node host/port, or browser WebSocket connectivity is wrong.
- The Settings page still edits server.properties as a text file rather than offering a structured form for each setting.
- Editor behavior requires deployment of the matching PHP extension allowlist and all local CodeMirror assets; upload only the frontend files without the bridge change and some file types will still be rejected.

## Evidence

The UI details are visible in Fern’s `index.html`, `app.js`, and `styles.css`. The backend contracts and permission limits are described separately in [Backend and operations](04-backend-and-operations.md).