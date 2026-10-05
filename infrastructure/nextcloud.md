# Nextcloud

Deployment and backup validation recorded on 2026-10-05 from the setup notes
and operator confirmation. No live configuration was exported for this update.

## Host and stack

Nextcloud runs on an Ubuntu VM at `10.10.50.50` with Docker Compose.

| Component | Purpose |
|---|---|
| Nextcloud | Web application and file access |
| PostgreSQL | Application database; container `nextcloud-db` |
| Redis | Cache/locking service |
| cron | Nextcloud background jobs |
| cloudflared | Cloudflare Tunnel connector |
| TrueNAS NFS | Persistent user data and separate database/config backup storage |

The VM's hypervisor placement, subnet prefix, gateway, VLAN ID, image tags,
and exact container mounts are not established by the available notes.
The address alone is not evidence of a VLAN 50 configuration.

## Access and application validation

The confirmed public path is:

```text
Internet client
    -> Cloudflare -> Cloudflare Tunnel / cloudflared
    -> Caddy -> Nextcloud on the Ubuntu VM
```

The deployment has validated real client IP forwarding through the proxy
chain, proxy trust, HSTS, and web/mobile/desktop login. Caddy's modular
configuration includes `nc.caddy`; the earlier proxy notes also record
HTTPS overwrite settings, secure cookies, and the corrected HTTPS redirect.
See [Caddy](caddy.md).

SMTP was tested using `nextcloud@longn.ca`. SMTP credentials, tunnel tokens,
and database secrets remain private. Secrets were moved into a host-local
`.env`; neither that file nor the live `config.php` is a repository artifact.
No sanitized Compose export is available yet, so this page records the
running components without inventing a deployable configuration.

## Storage and automated backups

| TrueNAS dataset | Purpose |
|---|---|
| `protected/Nextcloud` | Live Nextcloud user files, accessed over NFS |
| `protected/NextcloudBackups` | PostgreSQL dump and configuration backup sets, accessed over NFS |

Automated PostgreSQL/config backups write timestamped directories beneath
`/mnt/nextcloud-backups` on the Ubuntu VM. Backup NFS access uses the dedicated
`nextcloudbackup` identity. Its numerical UID/GID, export mapping, and the
backup job schedule were not retained in the available text and need a
sanitized configuration inventory before they can be documented precisely.

Each tested backup set contains:

- `nextcloud.sql` — PostgreSQL dump.
- `config.php` — Nextcloud configuration.
- `compose.yml` — stack definition.
- `.env` — private environment configuration.

These backup sets contain secrets and must stay outside Git. The database
dump/config set and the live user-data dataset cover different parts of
recovery; a database restore alone does not recover uploaded files.

## Snapshot policies

TrueNAS periodic snapshot tasks were created and enabled. The setup review
identified retention values of four hours, two days, two weeks, eight weeks,
and six months. Dataset names were truncated in the screenshot, so the exact
task-to-dataset mapping and first successful executions remain to be checked.

The policy proposed during setup was:

| Dataset | Proposed schedule and retention; mapping still to verify |
|---|---|
| `protected/Nextcloud` | Frequent/hourly, daily, and weekly snapshots with short retention tiers |
| `protected/NextcloudBackups` | Daily retained two weeks; weekly eight weeks; monthly six months |

These are the intended tiers, not a verified export of every snapshot task.

## Restore test: passed

The operator confirmed that the following non-destructive test succeeded:

1. Selected the newest backup under `/mnt/nextcloud-backups`.
2. Created `nextcloud_restore_test` inside the existing PostgreSQL container.
3. Loaded `nextcloud.sql` into that temporary database.
4. Listed restored tables and queried row counts from `oc_users` and
   `oc_filecache` successfully.
5. Checked that `config.php`, `compose.yml`, and `.env` existed in the backup.
6. Removed the temporary test database, leaving the live database intact.

This validates dump restoration, queryability, configuration-file presence,
and access to the NFS backup location. It does not establish a complete
application rebuild or a coordinated user-files/database point-in-time
recovery. A future isolated full recovery drill should test those together.

## Follow-up

See the [roadmap](../docs/Roadmap.md) for snapshot verification, the remaining
sanitized inventory, 2FA, monitoring/log retention, and a full recovery drill.
