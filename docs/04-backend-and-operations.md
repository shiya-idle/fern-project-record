# Backend and operations

## PHP bridge

`api.php` is the boundary between the browser and Pterodactyl. It loads the Fern configuration from `FERN_CONFIG` or a server-side path, holds the Panel URL and API credentials, starts a protected PHP session, validates actions, and proxies approved requests. The browser does not receive the Pterodactyl Client or Application API keys.

The bridge separates two API roles:

- **Client API key** for the configured server’s status/resources, power, console commands, WebSocket-token request, files, uploads/downloads, and backups.
- **Application API key** only for optional one-time server creation and the guarded Geyser/Floodgate setup flow. This key has broader privileges and must remain private and be limited to only needed permissions.

The code uses HTTPS for Panel calls, disables redirects, verifies TLS and hostnames, validates secure WebSocket schemes, and checks upload/download hosts where applicable. External plugin download handling is restricted to the official Geyser download host, verifies metadata, file size/archive signature, and SHA-256 before upload.

## Authentication and request protections

The owner signs in with the configured password hash. Successful login regenerates the session ID and issues a random CSRF token. Non-GET owner requests require the session CSRF header. The code applies a lockout after repeated failed login attempts. Logout also requires CSRF. Session cookies are HttpOnly and SameSite=Strict, with Secure set when HTTPS is detected (including the configured forwarded-proto path).

Public player routes use a separate player CSRF token. Whitelist submission is limited by session and remote-address cooldowns. The player portal is not equivalent to owner authentication. The app’s local profile data is convenience data only.

`safePath()` rejects path traversal, NULs, and overlong paths. File editing accepts only an explicit text-extension list and limits writes to 1 MiB. Uploads are capped in the bridge at 100 MiB (actual cPanel/PHP upload settings may be lower). Commands are restricted to one line and a maximum length. Backup identifiers are format-checked. These application checks complement, but do not replace, Pterodactyl permissions and server-side access controls.

## One-time Paper provisioning

Provisioning is disabled by default. If explicitly enabled and correctly configured, Fern accepts only a server name. The bridge pins the owner, node, nest, Paper egg, Docker image, environment, and resource limits; checks required unassigned allocations; uses a fixed external ID; takes a lock to prevent concurrent creation; checks for an existing server; confirms the client-key owner; and stores the created server identifier in the session. Fern is not intended to create arbitrary servers, switch eggs, modify limits freely, or delete servers.

The intended allocations are Java TCP 25565 and Bedrock UDP 19132. The example documents Java 25, but the bridge’s general validation checks that the Docker image is listed on the selected egg; it does not prove the active container is running Java 25. The example file contains placeholders and must never be treated as a real deployment config.

## Geyser/Floodgate setup

The bridge’s crossplay setup path checks the intended provisioned server, its owner, and the Paper installation state. It creates `plugins/` if needed and downloads official Geyser-Spigot and Floodgate-Spigot JARs after verifying their metadata and hashes. Uploads go through Pterodactyl’s signed upload URL only when it matches the Panel host over HTTPS.

A current nuance: the implementation returns after installing one missing plugin, so when both are absent, a later call may be needed to install the second. Confirm that both JARs are present, Floodgate auth/prefix settings are right, UDP 19132 is open and allocated, and a Bedrock player can actually join.

## Whitelisting and Bedrock XUID handling

Java submissions validate a 3–16 character Java name and call Mojang’s profile API to check that the supplied UUID and name agree. Bedrock submissions validate the Gamertag and hexadecimal XUID, pad the XUID to 16 hex digits, query Geyser’s `bedrock_or_java` utility endpoint using the configured Floodgate prefix, compare the XUID to the UUID suffix, and create the Floodgate whitelist entry from the verified UUID.

A bug found during this project: the bridge once replaced spaces in the submitted Gamertag with underscores before its Geyser API lookup. Floodgate’s *in-game username/command* representation replaces spaces with underscores, but the Geyser lookup endpoint expects the username as submitted. The bridge was changed to pass the original Gamertag. The API was tested in the sandbox: the exact name with a space returned a UUID suffix matching the provided XUID, while the underscore spelling did not resolve in Geyser’s cache. This is a local code fix; deployment to cPanel and a successful portal resubmission were not confirmed at the time of this archive.

Authoritative references: [Geyser UUID utility API](https://geysermc.org/wiki/api/api.geysermc.org/global-api-web-api-utils-controller-get-bedrock-or-java-uuid/) and [Geyser FAQ](https://geysermc.org/wiki/geyser/faq/). The FAQ’s underscore guidance applies to Floodgate username/command usage and should not be misapplied to the API lookup input.

## Server properties and Java/Bedrock chat

Fern’s Settings page currently reads and writes the actual `server.properties` as a text editor. Common requested values discussed for the friends server included survival/normal difficulty, non-hardcore, enabled PvP by default unless the owner chooses otherwise, 8-chunk view distance, 6-chunk simulation distance, 16-block spawn protection, online authentication, whitelist enabled, and a group-sized player cap. These were recommendations, not a verified production configuration. Existing `level-name` and `level-seed` should not be changed casually.

For Bedrock chat through Geyser on a Java server, the official Geyser secure-chat guidance recommends `enforce-secure-profile=false` in `server.properties` because Bedrock does not implement Java chat signing. Keep `online-mode=true`. If the Bedrock client displays an account-settings restriction, that is an Xbox/Microsoft account privacy setting, not a server permission; the player/guardian must allow text and multiplayer communication. Sources: [Geyser secure chat](https://geysermc.org/wiki/geyser/secure-chat/) and [Minecraft chat access help](https://help.minecraft.net/hc/en-us/articles/43413776295437-Troubleshoot-Minecraft-Java-Edition-Chat-Access).

## cPanel deployment checklist

1. Upload the public site files to the intended cPanel document root.
2. Keep the filled private configuration outside `public_html`; only use the example file as a template.
3. Set the PHP runtime/extensions and outbound access needed for cURL, PDO MySQL, Pterodactyl HTTPS, Geyser/Mojang HTTPS, and the status query where needed.
4. Create the metrics database and run the schema separately; never place the DB under public_html.
5. Configure HTTPS and trusted reverse-proxy headers; do not accept attacker-controlled forwarded-proto values.
6. Verify the Pterodactyl Panel URL, Wings TLS/FQDN, Client API permissions, optional Application API scope, server identifier, node/egg/allocation values, and plugin setup.
7. Confirm TCP/UDP firewall/allocations independently in AWS and Pterodactyl.
8. Test login, CSRF, status, power, console, file operations, settings save/reload, backup, Java whitelist, Bedrock whitelist, roster, metrics, and failure states.

This checklist is a verification plan, not proof that these steps were completed.