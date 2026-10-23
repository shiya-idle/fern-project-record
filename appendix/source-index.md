# Source index

This archive was prepared from the following sources. Repository-relative paths are easier to use than the original sandbox paths. Local source may contain uncommitted edits; the GitHub default branch may therefore differ from the snapshot reviewed here.

## Fern website

Repository: [shiya-idle/craft-control-panel](https://github.com/shiya-idle/craft-control-panel)

| Local source path | Purpose |
|---|---|
| `craft-control/README.md` | Deployment topology, requirements, configuration, provisioning, security caveats, pending replay endpoints. |
| `craft-control/index.html` | Owner shell, login, portal navigation, local CodeMirror asset loading. |
| `craft-control/app.js` | API wrapper, owner/player flows, charts, console, files, settings, editor, whitelist and roster UI. |
| `craft-control/styles.css` | Fonts, palette, responsive owner/portal layouts, console/editor appearance. |
| `craft-control/api.php` | PHP routing, Pterodactyl requests, sessions, CSRF, provisioning, crossplay, files, whitelist, metrics ingestion. |
| `craft-control/config.example.php` | Private config template, example defaults, disabled-by-default provisioning/storage options. Never use it as a secret source. |
| `craft-control/.htaccess` | Apache HTTPS redirect, directory listing behavior, and denied files/headers. |
| `craft-control/sql/fern_metrics.sql` | Session, event and replay-metadata schema. |
| `craft-control/assets/codemirror/` | Local editor core, modes, addons, CSS and licensing files. |

## Related plugin/review sources

| Location or repository | Purpose |
|---|---|
| [kammonusinno-ops/forge-solo-subseason](https://github.com/kammonusinno-ops/forge-solo-subseason) | Separate Paper gameplay-plugin source repository. |
| `/home/ubuntu/forge-solo-subseason-balance-review.md` | Static review; records defects and proposed remediation. It explicitly made no gameplay changes. |
| `/home/ubuntu/fern-replay-capture/README.md` | Replay prototype scope and intended behavior. |
| `/home/ubuntu/fern-replay-capture/PAUSED_PROGRESS.md` | Pause instruction, local test/build report, playback/runtime limitations, and eight blockers. |
| `/home/ubuntu/fern-replay-capture/docs/COMPATIBILITY.md` | Paper/Java and Flashback-format design notes. |
| `/home/ubuntu/fern-replay-capture/src/main/java/com/fern/replay/` | Plugin lifecycle, capture, replay format, storage, privacy, and Fern queue source. |
| `/home/ubuntu/fern-replay-capture/src/test/java/com/fern/replay/ReplayCoreTest.java` | Structural, retention and event/signature tests. |

The replay project was local in the reviewed sandbox and no public Git remote was confirmed during archive preparation. Its local paths are evidence provenance, not links available to all repository readers.

## Selected external references

- [GeyserMC UUID utility API](https://geysermc.org/wiki/api/api.geysermc.org/global-api-web-api-utils-controller-get-bedrock-or-java-uuid/) — Bedrock/Java UUID lookup endpoint used to investigate name/XUID verification.
- [GeyserMC secure chat guide](https://geysermc.org/wiki/geyser/secure-chat/) — secure-profile setting relevant to Bedrock chat on Java servers.
- [GeyserMC FAQ](https://geysermc.org/wiki/geyser/faq/) — Floodgate username/underscore behavior; distinguish in-game username formatting from API lookup input.
- [Minecraft Java chat troubleshooting](https://help.minecraft.net/hc/en-us/articles/43413776295437-Troubleshoot-Minecraft-Java-Edition-Chat-Access) — account-level communication restrictions.
- [Xbox privacy settings](https://account.xbox.com/Settings) — account controls for communication and multiplayer.

## Evidence conventions

- Code is used to describe behavior visible in that code, not to claim successful deployment.
- READMEs and example config describe intended design and prerequisites; they are not live configuration proof.
- `PAUSED_PROGRESS.md` is a historical checkpoint, not a fresh runtime test.
- The gameplay balance review is a static review and proposed fix list, not evidence of completed fixes.
- Conversation history is labeled as historical context when no source file provides equivalent evidence.
- No private config, credentials, API keys, HMAC values, private keys, player IDs, or private host addresses are included.