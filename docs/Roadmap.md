# Roadmap

Status reconciled on 2026-11-13. The [main roadmap](../README.md#current-roadmap)
also tracks the wider lab backlog.

## Wazuh / UniFi

- [x] Receive and filter UniFi Security/Audit CEF events on Infra01.
- [x] Forward via Wazuh Agent and confirm an indexed audit test alert.
- [x] Test rules `100201`/`100202` and combined-decoder source-IP extraction.
- [ ] Complete full/optional-field decoding, deploy it, and verify indexed fields.
- [ ] Validate event `544`, detection tuning, and raw/filtered log retention.
- [ ] Inventory Wazuh endpoint coverage, versions, persistence, and backups.

## Applications and operations

- [x] Implement and verify Jellyfin Web theme, personalized home rows, genre/drama collections, and IMDb ranking rows.
- [x] Record pre-change Jellyfin backup and retain original theme settings.
- [ ] Verify TV-client rendering and current server/plugin versions.
- [ ] Complete and verify weekly Rotten Tomatoes lists and recurring Seerr request automation.
- [x] Record Paperless-ngx CT 104 and local Ollama metadata suggestions.
- [ ] Test controlled automatic Paperless metadata application and document backups.
- [x] Verify native RustDesk access and Cloudflare-protected web UI.
- [ ] Resolve and externally test RustDesk browser WebSocket sessions.
- [x] Complete Home Assistant Sections layout with full-width weather strip.
- [ ] Inventory current HA host/IP, integrations, and backups.
- [x] Reconcile media-stack reference with host-local VPN env file and healthy dependency.
- [ ] Verify a controlled media-stack update and any proposed Gluetun startup-tolerance override.
- [ ] Confirm extended SMART result before treating `media-extra` pool creation as complete.
- [ ] Reconcile Caddy placement/internal DNS and current infrastructure host roles.
- [ ] Benchmark Bonsai locally before adding it to the deployed model inventory.

See [operations notes](Operations.md) for the source review and evidence limits.
The earlier AD and Nextcloud open items below have no newer completion evidence.

## Active Directory

- [x] Deploy `corp.longn.ca` with Windows Server 2025 DC01/DC02 and WIN11-01.
- [x] Verify LDAPS on both DCs using Smallstep certificates.
- [x] Schedule renewal every four hours as SYSTEM; successful task results reported.
- [x] Require SMB signing, set `LmCompatibilityLevel = 5`, and enable NTLM auditing.
- [x] Verify Kerberos between domain members and delegated workstation admin group membership.
- [ ] Record effective LDAP signing/channel-binding policy on both DCs.
- [ ] Confirm LDAP diagnostic auditing and collect compatibility evidence.
- [ ] Inventory LDAP consumers and test WIN11-01 plus third-party clients.
- [ ] Plan signing/channel-binding enforcement only after compatibility is proven.
- [ ] Verify renewed certificate expiry and the certificate served by each DC.
- [ ] Review remaining workstation local-admin membership and RDP rights.
- [ ] Revisit NTLM blocking after resolving the non-domain PC's NTLMv2 RDP dependency.

## Nextcloud

- [x] Deploy Ubuntu VM `10.10.50.50` and Nextcloud/PostgreSQL/Redis/cron/cloudflared stack.
- [x] Validate Cloudflare Tunnel -> Caddy -> Nextcloud, client IP forwarding, proxy trust, and HSTS.
- [x] Test web/mobile/desktop login and SMTP via `nextcloud@longn.ca`.
- [x] Move secrets to host-local `.env`.
- [x] Use TrueNAS NFS for user data and automated DB/config backups.
- [x] Establish dedicated `nextcloudbackup` NFS identity.
- [x] Create and enable periodic snapshot tasks.
- [x] Restore the SQL dump to a temporary DB, query tables, and check configuration files.
- [ ] Verify exact snapshot dataset mappings, schedules, retention, and successful first runs.
- [ ] Capture sanitized Compose/version inventory, backup schedule, NFS identity mapping, and VM network details.
- [ ] Document and perform an isolated full application/files/database recovery drill.
- [ ] Consider 2FA enforcement and define log retention/monitoring.

Details: [AD lab](../infrastructure/active-directory.md) and
[Nextcloud](../infrastructure/nextcloud.md).
