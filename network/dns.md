# DNS

## Internal DNS

Gateway
10.10.10.1

Resolver
UCG Fiber

---

## External Access

Provider
Cloudflare Tunnel

Applications

vault.longn.ca
→ TrueNAS :30032

immich.longn.ca
→ TrueNAS :30041

nc.longn.ca
→ TrueNAS :30027

## Notes

Current implementation:
- Cloudflare Tunnel points directly to application IPs.
- Application IP changes require updating Cloudflare routes.

Future state:
- Traefik
- Wildcard certificate
- Dynamic service discovery