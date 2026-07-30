# Homelab Roadmap

## Foundation

- [x] VLAN architecture
- [x] Infrastructure IP plan
- [x] GitHub documentation
- [x] Homepage
- [x] Portainer
- [x] Uptime Kuma
- [x] AdGuard Home
- [x] Network-wide AdGuard deployment

## Core Infrastructure

- [x] Internal DNS naming
- [x] Caddy reverse proxy
- [x] Internal TLS (Caddy)
- [x] Reverse proxy for core services

## Identity and Access

- [ ] Internal PKI (optional)
- [ ] Authentik
- [ ] Initial SSO integration
- [ ] Expand SSO to supported services

## Observability

- [ ] Prometheus
- [ ] Grafana
- [ ] Node Exporter
- [ ] Wazuh

## Applications

- [x] Jellyfin
- [ ] Immich
- [ ] Nextcloud
- [ ] Vaultwarden
- [ ] Paperless-ngx
- [ ] Linkwarden
- [ ] Joplin Server

## Infrastructure Automation

- [ ] Docker Compose repository
- [ ] Configuration backups (Git)
- [ ] Automated config backup script
- [ ] Infrastructure restore documentation

## Future

- [ ] External HTTPS migration
- [ ] Wildcard certificates
- [ ] High Availability
- [ ] Off-site backups

## 2026-07-30

### Added
- Modular Caddy configuration
- Reusable Cloudflare TLS snippet
- Individual site configuration files

### Changed
- Split monolithic Caddyfile into modular structure
- Improved maintainability of reverse proxy configuration

### Fixed
- Homepage reverse proxy configuration
- Nextcloud HTTPS redirect issue
- Nextcloud trusted proxy configuration
- Secure cookie handling