# RustDesk Remote Access

Status recorded 2026-11-13 from the October troubleshooting notes.

| Capability | Recorded state |
|---|---|
| Native RustDesk client | Remote access working |
| Server | RustDesk LXC; ID/relay hostname `rustdesk.longn.ca` |
| Web portal | Served through Cloudflare Tunnel with Cloudflare Access authentication |
| Browser client | Loads at `https://rustdesk-web.longn.ca/webclient/#/` |
| Browser remote-control session | Unresolved WebSocket connection issue |

The browser client was constructing direct WebSocket connections to
`rustdesk.longn.ca:21118` (and the related relay port `21119`) while the
intended Cloudflare HTTPS path used port 443. Loading the web UI and passing
Cloudflare Access authentication did not establish a working remote-control
session. The latest note still reports the WebSocket issue after VPN testing.

The next step is to verify the browser client's supported endpoint configuration
and test an external session through the intended path. Exact LXC identity,
server versions, published ports, firewall exceptions, and backup coverage
still need a sanitized inventory. Native access and browser access must remain
separate completion items.

Passwords, unattended-access credentials, device IDs, private keys, and tunnel
tokens are intentionally omitted.
