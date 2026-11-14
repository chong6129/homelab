# Operations Notes

Reviewed 2026-11-13. These are outcomes from the available setup conversations,
not a fresh host or network audit. Older documentation remains dated evidence
where no newer configuration was captured.

## Home Assistant dashboard

The October dashboard work confirms Home Assistant is in use again. A native
Sections dashboard with a glass theme and a full-width weather strip was
accepted by the user. The strip showed six forecast days; a longer forecast
was not demonstrated. Further Front Door and Nest card styling was proposed.

The current HA host, IP, integrations, and backup configuration are not captured
in this update. Do not reuse Infra02's `10.10.10.189` address based on its old
Home Assistant label. The documented monitoring deployment used that address.

## Browser access incident

November troubleshooting narrowed intermittent Jellyfin/Nextcloud/Vaultwarden
connection failures to browser/profile behavior rather than proving a general
backend outage. Jellyfin worked in a fresh Chrome profile. In a later Brave
test, Nextcloud loaded after a complete browser restart with Bitwarden enabled,
and the user confirmed successful Vaultwarden authentication.

Profile rebuilding and extension reinitialization restored access in the
recorded tests. QUIC was disabled during the later test; re-enabling it was
proposed but not confirmed. The root cause remains undetermined. Do not
describe HTTP/3, Caddy, or Bitwarden as a proven cause.

The later tests identify Nextcloud's public hostname as `nc.longn.ca`.
Jellyfin DNS was reported resolving internally to `10.10.10.4` during the
November investigation; the current Caddy host/placement should be reconciled
with the older Infra01 description before changing network inventory tables.

## Storage expansion: pending

The November 6 storage review describes `protected` as a two-drive 4 TB Exos
mirror and `staging` as the existing 8 TB SkyHawk media pool. Existing Jellyfin,
media-stack, and SMB movie/TV paths were to be preserved.

A separate single-disk `media-extra` pool using an available 4 TB WD Red Plus
was proposed for temporary capacity. The last user-supplied SMART result still
showed an extended self-test in progress, with 90% remaining. No passing result
or new-pool creation is recorded. Four 8 TB drives were discussed as a future
upgrade; purchase discussion does not establish installed capacity.

Keep SMART completion, any pool creation, and application mount integration
open. The current repository must not present `media-extra` as deployed.

## Local AI experiments

Bonsai 2 27B was discussed for testing alongside the existing Ollama/GTX 1070
setup. Runtime compatibility and performance were not tested in the available
record. No model installation, GPU replacement, or VM relocation is claimed.

## Evidence and gaps

Reviewed sources include “Assess Wazuh Deployment Plan,” “Customize Jellyfin
home screen,” “Fix Media Stack Update,” “Cloudflare Tunnel Update,”
“Troubleshoot Vaultwarden TLS,” “HA Dashboard,” “Plan Homelab Projects,”
“Diagnose Browser SSL Issues,” and “Run Bonsai 2 Locally.” Some retrieved
histories contain only their latest turns; screenshot-only details were not
reconstructed. No new result was found for the outstanding AD LDAP enforcement
or full Nextcloud recovery work, so those checklists remain open.
