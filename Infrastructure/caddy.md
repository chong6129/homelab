Modular Caddy configuration
/etc/caddy/sites/
/etc/caddy/snippets/
Cloudflare TLS snippet
One file per application
Validation workflow (caddy validate before reload)

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

    overwriteprotocol=https
overwritehost
overwrite.cli.url
trusted_proxies
Verified secure cookies
HTTPS redirect fixed