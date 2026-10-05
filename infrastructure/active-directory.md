# Active Directory Lab

Status recorded on 2026-10-05 from the lab setup and LDAP-hardening notes.
This is a record of reported validation, not a fresh inspection of the DCs.

## Environment

| System | Address | Role |
|---|---|---|
| DC01 | `10.66.6.10` | Windows Server 2025 domain controller |
| DC02 | `10.66.6.11` | Windows Server 2025 domain controller |
| WIN11-01 | Not recorded here | Domain-joined Windows 11 test client |

The AD domain is `corp.longn.ca`. Both DCs are on the Security-Lab subnet.
Kerberos authentication between domain members has been verified.
The delegated `CORP\Workstation Local Admins` group was confirmed in
WIN11-01's local Administrators group through Group Policy Preferences.

## Completed and validated

| Control | Recorded result |
|---|---|
| LDAPS | TCP 636 working on DC01 and DC02; tested with `ldp.exe` |
| Internal PKI | Certificates issued by self-hosted Smallstep `step-ca`; both DCs trust its root CA |
| Certificate renewal | Scheduled every four hours on both DCs, running as SYSTEM; `LastTaskResult = 0` reported |
| SMB signing | Required |
| LAN Manager compatibility | `LmCompatibilityLevel = 5` |
| NTLM visibility | NTLM auditing enabled |
| Domain authentication | Kerberos verified between domain members |

Successful scheduled-task results are the recorded renewal evidence. Future
checks should also confirm certificate expiry and the certificate actually
presented by each LDAPS endpoint after renewal.

A non-domain-joined management PC still legitimately uses NTLMv2 for RDP.
Global NTLM blocking has **not** been enabled and remains deferred while that
dependency exists.

## LDAP signing and channel binding: compatibility work pending

Working LDAPS does not establish that signing or channel-binding enforcement
has been validated. The latest hardening discussion stopped at an audit-first
plan; it contains no returned policy values or completed compatibility results.
Do not describe either enforcement rollout as complete.

Next steps are to capture effective policy on **both** DCs, including
`LDAPServerIntegrity`, `LDAPServerEnforceIntegrity`, and
`LdapEnforceChannelBinding`, and compare those readings with computer GPO
results. Record the LDAP diagnostic logging state before changing it.
Windows Server 2025's separate signing-enforcement policy must be included
in that review; do not infer the effective state from a legacy setting alone.

The planned discovery work includes Directory Service event review (2889,
3039, 3074, and 3075), an inventory of real LDAP consumers, and tests from
WIN11-01 and each application/appliance. Record transport, authentication
method, certificate validation, signing, and channel-binding compatibility.
Audit setup and event collection are still unconfirmed.

Only after compatibility is demonstrated should an enforcement change be
planned, with before/after evidence and rollback settings. See the
[roadmap](../docs/Roadmap.md) for the outstanding checklist.

## Scope and records

No CA private keys, enrollment credentials, PFX files, passwords, or raw
authentication logs belong in this repository. Exact task scripts and PKI
configuration have not been imported. Authentik-to-AD and Nextcloud-to-AD
integration are not claimed as deployed.
