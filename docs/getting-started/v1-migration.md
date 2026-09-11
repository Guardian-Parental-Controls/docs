# Migrating from 0.x to 1.0

Guardian 1.0 splits the server, agents, documentation, translations, and release
catalog into separate repositories. It also replaces GitHub Releases discovery
with the compiled [versions feed](https://guardian-parental-controls.github.io/versions/feed.json).

This is a breaking upgrade. A 0.x agent does not automatically upgrade to 1.0.

## Server

1. Back up the database and `.env`.
2. Pull `ghcr.io/guardian-parental-controls/platform-server:v1.0.0`.
3. Remove `TIMEKPR_GITHUB_RELEASE_REPO` and
   `TIMEKPR_RELEASE_CACHE_TTL_SECONDS`.
4. Optionally set `GUARDIAN_VERSIONS_URL` when using a private fork or mirror.
5. Start the server and confirm the dashboard is healthy before reinstalling
   agents.

## Managed devices

- Linux: run the installer URL in the `agent-linux` feed entry.
- Windows: download the MSI through the server pairing page.
- Android: provision the 1.0 Device Policy Controller APK again using the
  server-generated QR code.

Existing household data remains on the server. Reinstalling an agent may
require approving the device again if its stored enrollment token is removed.

## Offline and forked deployments

Mirror `feed.json` and every referenced artifact, preserving checksums. Set
`GUARDIAN_VERSIONS_URL` to the mirrored feed before pairing devices.
