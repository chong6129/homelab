# Wazuh and UniFi Security Telemetry

Status documented 2026-11-13 from the November 5–6 setup and test records.
This update reviews recorded results; it does not inspect the running hosts.

## Implemented path

```text
UCG Fiber syslog export
    -> Infra01 rsyslog receiver
    -> filtered Security/Audit CEF events
    -> Wazuh Agent forwarding
    -> Wazuh Manager on Infra02 (Docker)
    -> indexer -> Wazuh dashboard
```

The setup record confirms an indexed level-4 test alert associated with
Infra01. This establishes receipt, filtering, forwarding, decoding, alert
generation, and visibility in the dashboard for the tested audit event.
It does not establish coverage of all gateway events or all lab endpoints.

## Decoder and rule validation

| Item | Recorded result |
|---|---|
| Custom decoder | `unifi-cef` recognizes the UniFi CEF header |
| Rule `100201` | IDS/IPS “Threat detected and blocked” test generates level 7 |
| Rule `100202` | Audit-event test generates level 4; indexed dashboard result recorded |
| Source IP extraction | Combined header/source-IP decoder test extracts `srcip` successfully |
| Additional IDS fields | Work in progress; full extraction and live rollout not confirmed |

The latest returned `wazuh-logtest` output contains the event ID, event name,
vendor severity, source IP, and the expected rule `100201` result. Earlier
sibling-decoder attempts matched the header but failed to extract the extra
network fields. A combined expression was then proposed for protocol, ports,
action, destination, and signature fields; its result is not in the available
record. Test-file edits must not be described as a completed Manager rollout.

UniFi CEF severity and Wazuh rule level are separate values. The recorded
integration preserves the distinction rather than treating them as equivalent.

## Outstanding work

- Complete extraction tests against representative full IDS/IPS and audit events,
  including events with optional or missing fields.
- Validate configuration, deploy the completed decoder, and confirm a new
  indexed alert contains the expected fields.
- Test administrative-access event `544` handling.
- Tune repetitive P2P signature `2008581` without losing useful detections.
- Confirm rotation and retention for raw syslog and filtered CEF files on Infra01.
- Inventory endpoint agents, persistent volumes, image versions, backup coverage,
  and alert routing before claiming broader SIEM coverage.

Decoder/rule files have not been copied into Git because the final live
configuration is not established. Raw events and endpoint identifiers are
omitted; only the integration structure and test outcomes are documented.
