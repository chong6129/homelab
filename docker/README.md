# Docker Configuration

This directory contains sanitized deployment references for the running
homelab. Secrets, tunnel tokens, database passwords, Authentik keys, and
exporter credentials must remain in host-local environment or configuration
files and must not be committed.

## Compose references

| File | Host/workload |
|---|---|
| [`infra01-core.yml`](compose/infra01-core.yml) | Portainer, Homepage, and Uptime Kuma |
| [`infra01-authentik.yml`](compose/infra01-authentik.yml) | Authentik server, worker, and PostgreSQL |
| [`infra02-monitoring.yml`](compose/infra02-monitoring.yml) | Prometheus, Grafana, Graphite Exporter, and PVE Exporter |
| [`node-exporter.yml`](compose/node-exporter.yml) | Host-mode Node Exporter used on both Raspberry Pis |
| [`truenas-media-stack.yml`](compose/truenas-media-stack.yml) | Sanitized TrueNAS custom media stack |

The TrueNAS catalog applications are generated and managed by TrueNAS rather
than deployed from these repository files. Their verified images, ports, and
storage mounts are documented in
[`infrastructure/truenas.md`](../infrastructure/truenas.md).

The media-stack reference requires `OPENVPN_USER` and `OPENVPN_PASSWORD` at
deployment time. Do not add their values to Git.
