# Architecture Diagrams

## November additions

```text
UCG Fiber -> Infra01 rsyslog -> Security/Audit CEF filter
    -> Wazuh Agent -> Manager on Infra02 -> indexer -> dashboard
    Indexed audit test: passed; extended decoder rollout: pending

Paperless-ngx (Proxmox CT 104) -> AI integration -> Ollama 10.10.10.249:11434
    Metadata suggestions: working; automatic application: pending

RustDesk native client -> ID/relay service: working
Browser -> Cloudflare Access/Tunnel -> RustDesk web UI: loads
    Browser WebSocket remote-control session: unresolved
```

These are recorded logical relationships, not a complete network or firewall
inventory. See [Wazuh](../infrastructure/wazuh.md),
[Paperless](../infrastructure/paperless.md), and [RustDesk](../infrastructure/rustdesk.md).

The following AD/Nextcloud relationships were recorded on 2026-10-05. They do not
assert hypervisor placement or undocumented firewall rules.

## Active Directory

```text
Smallstep step-ca
    | issues LDAPS certificates; both DCs trust the root
    +---- DC01: dc01.corp.longn.ca / 10.66.6.10
    +---- DC02: dc02.corp.longn.ca / 10.66.6.11
             ^
             | LDAPS on TCP 636 tested against both DCs
          WIN11-01 (domain member)

Both DCs: renewal tasks every 4 hours, SYSTEM, reported result 0
Domain: corp.longn.ca; Kerberos verified between domain members
LDAP signing / channel-binding rollout: compatibility evidence pending
```

## Nextcloud

```text
Client -> Cloudflare -> Tunnel/cloudflared -> Caddy -> Nextcloud
                                                     | Ubuntu VM 10.10.50.50
                                                     +-- PostgreSQL
                                                     +-- Redis
                                                     +-- cron background jobs
                                                     +-- SMTP: nextcloud@longn.ca
                                                     |
                                                     +-- NFS user data
                                                     |   TrueNAS: protected/Nextcloud
                                                     |
                                                     +-- Automated SQL/config backups
                                                         NFS identity: nextcloudbackup
                                                         TrueNAS: protected/NextcloudBackups

TrueNAS periodic snapshots protect the two datasets.
Temporary PostgreSQL restore test: passed; full recovery drill remains pending.
```

See [AD](../infrastructure/active-directory.md),
[Nextcloud](../infrastructure/nextcloud.md), and the
[physical network topology](../network/topology.md).
