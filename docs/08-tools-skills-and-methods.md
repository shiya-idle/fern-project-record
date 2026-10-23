# Tools, skills, and working methods

## How to read this register

The record separates tools that are visible in the preserved task history from tools merely available in the environment. Tool use is not proof of deployment. Historical shell activity from AWS/cPanel/Pterodactyl was largely user-operated; this document does not reconstruct exact commands or credentials. The private reasoning behind decisions is not reproduced; the archive preserves user-facing requirements, engineering decisions, evidence, and remaining risks.

## Project and implementation tools

| Tool or environment | Purpose recorded | Evidence/status |
|---|---|---|
| File and shell tools (`read`, `write`, `edit`, `exec`) | Inspect/edit PHP, JavaScript, CSS, HTML, SQL, Java, README/configuration; run validation and packaging. | Visible in task records for source review and CodeMirror work. |
| `git` | Check working-tree changes, branch, diff/whitespace, repository state. | Used in local Fern work; the checkout included uncommitted modifications. |
| GitHub CLI (`gh`) | Check repository/access and manage/push the documentation repository. | Used for the archive repository setup; GitHub account credential itself is not recorded. |
| Node.js | `node --check app.js` for JavaScript syntax validation. | Validation only; not a browser integration test. |
| PHP CLI | `php -l api.php` and config syntax lint. | Validation only; not a cPanel runtime test. |
| npm/tar/zip | Fetched CodeMirror 5.65.20 locally, extracted required assets, and packaged the cPanel update ZIP. | Local packaging; does not prove cPanel upload. |
| Gradle wrapper/JUnit | Built and tested the replay plugin and ran structural/retention/signature tests according to its pause checkpoint. | Historical checkpoint result, not rerun as part of this archive; no Flashback playback test. |
| AWS console and SSH client (Termius reported) | User-side VM, storage, security-group, and server setup. | Historical user-led actions; current cloud state not verified. |
| ReadySpace/cPanel interface | User-side PHP/site/database uploads and configuration. | Historical user-led actions; current site contents not verified. |
| Pterodactyl Panel UI and Wings service | Node, egg, server, allocation, API-key, file/console and daemon setup. | Historical user-led setup; current service health not verified. |

## Code and systems used

- **Frontend:** PHP 8.1+, vanilla JavaScript, HTML, CSS. The Fern UI is uploaded as static site files plus `api.php`; it is not a Manus WebDev project.
- **Editor:** locally bundled CodeMirror 5.65.20, selected modes and edit/search addons, local CSS/JS files; no CDN dependency for editing.
- **Game host:** Pterodactyl Panel v1, Wings, Docker, Paper, Java runtime; Geyser-Spigot and Floodgate-Spigot for Java/Bedrock crossplay.
- **Data:** MySQL/MariaDB for session/event/replay metadata and player-hours calculations.
- **Replay plugin:** Java/Paper, Netty packet capture, a custom Flashback archive writer, local retention, and a queued HTTP integration prototype.
- **Gameplay plugin:** Java/Gradle multi-module project for Paper, with separate domain modules and JUnit tests.

## Web research tools

`search` and `fetch` were used in the prior project conversation to read official Geyser and Minecraft/Xbox documentation related to Bedrock chat/secure profiles and player-account communication restrictions. Geyser’s username lookup API and FAQ were consulted for the Bedrock Gamertag/XUID verification path. The substantive external references are listed in the backend document and source index. The archive does not preserve every search query or claim that a search snippet alone was used as evidence.

## Skill register

The inherited task context names these as the most recently used project skills. Their precise contribution is recorded at the level supported by the history; a skill being read does not prove an operational change was completed.

| Skill | Project relevance recorded | Evidence caveat |
|---|---|---|
| `deep-research` | Source-based research for Geyser secure chat, Xbox/Microsoft account restrictions, Geyser UUID lookup behavior. | Supports external guidance; does not prove server configuration or a successful Bedrock connection. |
| `persistent-computing` | Guidance for VM-backed hosting and persistent server infrastructure. | No persistent-VM deployment state is validated by this archive. |
| `webdev` | Reviewed as a web-application development skill during project work. | User explicitly chose not to build in Manus WebDev; Fern was implemented as direct PHP/JS for cPanel. |
| `manus-config` | Listed in the inherited history as recently used for project/configuration workflows. | No specific connector or configuration mutation is asserted unless separately evidenced. |
| `data-analysis` | Relevant to session metrics, retention, and player-hours chart; reread while preparing this archive. | No separate statistical study or spreadsheet is claimed; the chart is front-end rendering backed by SQL. |
| `workflow-composer` | Read and used to coordinate the independent source-area review for this archive. | Applies to the archival task, not to original Fern code development. |

## Validation and quality checks

The preserved Fern editor task ran JavaScript syntax checking, PHP linting, a CodeMirror asset existence check, license presence checks, and `git diff --check`, then produced a source ZIP. These checks catch syntax, missing assets, licensing-file omissions, and whitespace errors. They do not verify file permissions, API behavior, authentication, live cPanel configuration, or Minecraft runtime behavior.

The replay checkpoint reports a clean Gradle test/build, plugin JAR/source JAR generation, and a custom Flashback structure check. It explicitly states that no compatible Flashback client opened the output, no Paper 26.3 runtime smoke test established compatibility, no Minecraft server was started, and no EULA was accepted.

The gameplay balance review is static. Helper unit tests and formula consistency do not validate the full Paper listener wiring, security boundaries, multiplayer balance, economy, or production readiness.

## Working method recorded

1. Translate the user’s goal into a constrained scope and define which system owns which responsibility.
2. Inspect current project files before changing them; preserve private configuration and avoid overwriting user-specific setup.
3. Use the established PHP/JavaScript visual style and identify backend contracts before changing UI behavior.
4. Validate syntax, assets, and local tests after edits; package a cPanel upload separately from source changes.
5. Distinguish source implementation from production verification and use explicit pause/authorization boundaries.
6. For project review, separate findings from fixes and provide a prioritized next-step list.

## Security and privacy of this register

Do not add credentials, API keys, HMAC secrets, DB passwords, private keys, filled configuration, player XUIDs, player passwords, or private host addresses. Do not store hidden chain-of-thought or an exact command log reconstructed from memory. User-visible outcomes and source evidence are sufficient for the project record.