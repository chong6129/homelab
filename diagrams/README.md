# Architecture Diagrams

Logical service relationships recorded on 2026-10-05. These diagrams do not
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
