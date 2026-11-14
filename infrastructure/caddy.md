# Caddy

The November browser investigation reported Jellyfin resolving internally to
`10.10.10.4`. The current Caddy host and network placement still need a
sanitized inventory; older Infra01 references are not proof of current placement.
Browser/profile recovery is documented in [operations notes](../docs/Operations.md).

Caddy uses modular configuration with one file per application and a shared
Cloudflare TLS snippet. Validate configuration with `caddy validate` before
reloading a deployment change.

```text
/etc/caddy/
├── Caddyfile
├── snippets/
│   └── cloudflare_tls.caddy
└── sites/
    ├── homepage.caddy
    ├── immich.caddy
    ├── jellyfin.caddy
    ├── nc.caddy
    ├── portainer.caddy
    ├── uptime.caddy
    └── vaultwarden.caddy
```

## Nextcloud proxy path

The validated public route is Cloudflare -> Tunnel/cloudflared -> Caddy ->
Nextcloud on Ubuntu VM `10.10.50.50`. Real client IP forwarding, proxy trust,
HSTS, secure cookies, and the corrected HTTPS redirect are recorded as tested.

The setup notes identify Nextcloud settings `overwriteprotocol=https`,
`overwritehost`, `overwrite.cli.url`, and `trusted_proxies`. Exact hostname,
upstream port, trusted proxy addresses, header rules, and HSTS duration are
not reproduced without a sanitized export of the live configuration.

See [Nextcloud](nextcloud.md) for the stack, storage, SMTP, and recovery status.
The repository's older `caddy/CaddyFile` is not a complete export of this
modular deployment. Cloudflare credentials and tunnel tokens remain private.
