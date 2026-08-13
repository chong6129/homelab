Long's Enterprise Homelab

Enterprise-inspired self-hosted infrastructure focused on
security, automation, monitoring, and infrastructure engineering.

# Current Infrastructure

## Network
- UCG Fiber
- UniFi

## Compute
- Raspberry Pi 5 (Infra01)
- Dell OptiPlex 3050 (Proxmox)
- TrueNAS SCALE

---

# Current Services

## Infra01
- Homepage

## TrueNAS SCALE
- Immich
- Jellyfin
- Jellystat
- Nextcloud
- Vaultwarden
- Firefly III
- Cloudflared
- Cloudflared (Immich)
- Cloudflared (Jellyfin)
- Cloudflared (Vaultwarden)

## Virtual Machines

### Proxmox
- Kali Linux

### TrueNAS
- Ubuntu LLM Server

---

# Planned Services

## Infra01
- Portainer
- Uptime Kuma
- Pi-hole

## Future
- Grafana (optional)
- Prometheus (optional)
- Loki (optional)

Ubuntu LLM VM
-------------
Username: chong
Password: (stored in Bitwarden)

GPU:
- GTX1070
- Audio Function
- CPU Mode: Host Passthrough

Static IP:
10.10.10.219

SSH Enabled

CUDA Version:
...

Ollama
Open WebUI
...
Internet
      │
Cloudflare DNS
      │
Caddy Reverse Proxy
      │
──────────────────────────────
Homepage
Jellyfin
Immich
Vaultwarden
Nextcloud
Portainer
Uptime Kuma

Future Roadmap
Foundation
──────────
✓ Docker
✓ Cloudflare
✓ Caddy
✓ TLS
✓ Reverse Proxy
✓ Split DNS

Identity
────────
□ Authentik
□ SSO
□ MFA

Monitoring
──────────
□ Grafana
□ Prometheus
□ Loki

Security
────────
□ Wazuh
□ CrowdSec
□ Fail2Ban

Automation
──────────
□ Ansible
□ GitHub Actions
□ Automatic backups

##Roadmap - 07/30/2026

✔ Infrastructure
✔ Cloudflare
✔ Caddy
✔ HTTPS
✔ Split DNS
✔ Docker Apps

⬇

1. Authentik
2. Jellyseerr + Arr stack
3. Immich AI
4. Grafana/Prometheus
5. Wazuh
6. CrowdSec
7. Paperless
8. Automation