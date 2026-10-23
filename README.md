# Fern Project Record

This repository is the detailed project archive for **Fern**, a custom-branded, single-server Minecraft hosting and management platform for a private group of friends. It records user-facing requirements, planning decisions, architecture, implementation history, tool and skill usage, evidence limits, current status, and known follow-up work.

It is a **documentation companion**, not the Fern application source. The source repositories and saved project notes remain the implementation references. No passwords, API keys, HMAC secrets, private configuration values, private keys, player credentials, or production IP addresses belong here. This repository is intended to be private.

## Project in one paragraph

Fern uses a minimalist green-and-white PHP/vanilla-JavaScript interface hosted on ReadySpace/cPanel. A server-side PHP bridge talks to a Pterodactyl Panel and Wings node on AWS, where the Paper Minecraft server runs. The product includes an owner dashboard, public player portal, Java/Bedrock crossplay and self-whitelist flow, file/configuration operations, console and backup hooks, and session/replay metadata. A separate Paper gameplay plugin and a separate Flashback replay-capture prototype are tracked independently. The replay work is paused; actual AWS-to-ReadySpace replay-file routing is not complete; and the gameplay plugin is test-only pending its balance/security findings.

## Archive map

| Document | Contents |
|---|---|
| [Planning and requirements](docs/01-planning-and-requirements.md) | Original goals, scope changes, recorded design rationale, and intended system boundaries. |
| [Architecture](docs/02-architecture.md) | cPanel, PHP bridge, Pterodactyl/Wings, Paper, Geyser/Floodgate, database, and storage paths. |
| [Fern interface](docs/03-fern-interface.md) | Owner dashboard, public portal, design system, file manager, editor, and UI status. |
| [Backend and operations](docs/04-backend-and-operations.md) | API routes, authentication, provisioning, file/backup limits, crossplay, and security controls. |
| [Metrics and replay](docs/05-metrics-and-replay.md) | Session schema, HMAC ingestion, player-hours chart, replay format, retention, privacy, blockers, and pause status. |
| [Forge Solo Subseason](docs/06-forge-solo-subseason.md) | Gameplay-plugin architecture, review findings, launch posture, and stabilization work. |
| [Timeline](docs/07-timeline.md) | Chronological project history, with uncertain historical details explicitly labeled. |
| [Tools and skills](docs/08-tools-skills-and-methods.md) | Tool and skill register, attribution/evidence levels, and working method. |
| [Status and verification matrix](docs/09-status-and-verification.md) | What is implemented, planned, paused, or unverified, plus evidence needed for release. |
| [Source index](appendix/source-index.md) | Source files and citations used to assemble this record. |

## Status vocabulary

- **Implemented** means behavior exists in reviewed source. It does not by itself mean it is deployed or working against production services.
- **Deployment-dependent** means source is present but requires valid cPanel configuration, credentials, a reachable service, or a prepared server.
- **Planned** means the user asked for or project notes describe future behavior not evidenced as implemented.
- **Paused** means work was explicitly halted and must not be resumed until its stated user cue.
- **Unverified** means there is not enough runtime or deployment evidence to assert the feature works in the target environment.
- **Finding/blocker** means a review or checkpoint identified a defect, missing capability, or release risk.

## Evidence and limitations

This record is assembled from checked-out source, project READMEs, the replay pause checkpoint, the gameplay balance review, and conversation history preserved in the inherited task context. It is not a live inspection of ReadySpace, AWS, Pterodactyl, Minecraft, or player accounts. Historical user-operated steps are not reconstructed as exact command transcripts. When source and prior context differ in what they prove, this archive preserves the more cautious status.

The replay prototype is explicitly paused. Do not start a server, accept a Minecraft EULA, deploy the replay JAR, push replay code, or continue replay implementation until the user says **“continue flashback.”** See [Metrics and replay](docs/05-metrics-and-replay.md).

## Related source projects

- Fern website source: [craft-control-panel](https://github.com/shiya-idle/craft-control-panel)
- Gameplay plugin source: [forge-solo-subseason](https://github.com/kammonusinno-ops/forge-solo-subseason)
- Replay capture: a separate local project was present in the working environment; a public remote was not confirmed during record preparation.

This repository does not contain private deployment configuration.