---
tags: [cdsa, htb, windows, event-logs]
---


> [!info] See also: [[Basics/Techniques/Windows/Active Directory|Active Directory (Basics)]]


Primary components of an Event in the Windows Event Log:
![[windows_event_basic_structure.png]]

## Key Event Logs

| Log | Path | What it captures |
| --- | ---- | ---------------- |
| Security | `%SystemRoot%\System32\winevt\Logs\Security.evtx` | Logons, object access, policy changes |
| System | `...System.evtx` | OS events, driver issues, service starts |
| Application | `...Application.evtx` | App-level events |
| PowerShell/Operational | `...Microsoft-Windows-PowerShell%4Operational.evtx` | Script execution (needs to be enabled) |
| Sysmon | `...Microsoft-Windows-Sysmon%4Operational.evtx` | Process creation, network, file events |

## High-Value Event IDs (Security Log)

| Event ID | Description |
| -------- | ----------- |
| 4624 | Successful logon |
| 4625 | Failed logon |
| 4648 | Logon with explicit credentials (runas) |
| 4656 | Object handle requested (file access) |
| 4698 | Scheduled task created |
| 4720 | User account created |
| 4732 | User added to local group |
| 4768 | Kerberos TGT requested (AS-REQ) |
| 4769 | Kerberos service ticket requested (TGS-REQ) |
| 4771 | Kerberos pre-auth failed |
| 4776 | NTLM authentication attempt |
| 7045 | New service installed |


## Logon Types (Event 4624)

| Logon Type | Description                        |
| ---------- | ---------------------------------- |
| 2          | Interactive (local keyboard)       |
| 3          | Network (SMB, net use)             |
| 4          | Batch (scheduled task)             |
| 5          | Service logon                      |
| 7          | Unlock workstation                 |
| 8          | Network cleartext (IIS basic auth) |
| 10         | RemoteInteractive (RDP)            |
| 11         | CachedInteractive (offline logon)  |
