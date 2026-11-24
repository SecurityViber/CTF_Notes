---
tags: [cdsa, htb, windows, event-logs]
---

> [!info] See also: [[Active Directory]] · [[Incident Handling]] · [[Links and Resources]]


## Event Structure

Primary components of an event in the Windows Event Log:

![[windows_event_basic_structure.png]]

## Key Event Logs

| Log | Path | What it captures |
| --- | ---- | ---------------- |
| Security | `%SystemRoot%\System32\winevt\Logs\Security.evtx` | Logons, object access, policy changes |
| System | `...System.evtx` | OS events, driver issues, service starts |
| Application | `...Application.evtx` | App-level events |
| PowerShell/Operational | `...Microsoft-Windows-PowerShell%4Operational.evtx` | Script execution (needs to be enabled) |
| Sysmon | `...Microsoft-Windows-Sysmon%4Operational.evtx` | Process creation, network, file events |
| Defender | `...Microsoft-Windows-Windows Defender%4Operational.evtx` | Malware detection and remediation |

## High-Value Event IDs

> [!warning] The IDs below are grouped by **what they tell you**, which is how the module presents them.
> That is not always the log they are stored in: `7045` is written by the Service Control Manager to the
> **System** log, and the Defender IDs live in the **Defender operational** log, not in `Security.evtx`.

### Security Log — logon and privileges

| ID | Event | Why it matters |
| -- | ----- | -------------- |
| 4624 | Successful logon | Establishes normal user behaviour; logons at odd hours or from unusual locations are a flag |
| 4625 | Failed logon | Multiple in a row signify a brute-force attack in progress |
| 4648 | Logon with explicit credentials (runas) | Anomalies indicate lateral movement |
| 4672 | Special privileges assigned to a new logon | An account logged on with super user privileges |
| 4771 | Kerberos pre-auth failed | Kerberos counterpart to 4625; volume suggests Kerberos brute force |
| 4776 | DC attempted to validate credentials | Covers successes and failures; repeated failures suggest brute force |

### Security Log — accounts

| ID | Event | Why it matters |
| -- | ----- | -------------- |
| 4720 | User account created | |
| 4732 | User added to local group | |
| 4738 | User account changed | Privileges, group memberships, settings. Unexpected changes signal account takeover or insider threat |
| 4768 | Kerberos TGT requested (AS-REQ) | |
| 4769 | Kerberos service ticket requested (TGS-REQ) | |

### Security Log — scheduled tasks (persistence)

| ID | Event | Why it matters |
| -- | ----- | -------------- |
| 4698 | Scheduled task created | Attackers use tasks to maintain access and run code |
| 4700 | Scheduled task enabled | |
| 4701 | Scheduled task disabled | |
| 4702 | Scheduled task updated | Hijacking an existing legitimate task shows up here, not as 4698 |

### Security Log — audit tampering

| ID | Event | Why it matters |
| -- | ----- | -------------- |
| 1102 | Audit log cleared | Destroys evidence already collected |
| 4719 | System audit policy changed | Stops future evidence being collected |

### Security Log — object, share and network access

| ID | Event | Why it matters |
| -- | ----- | -------------- |
| 4656 | Handle to an object requested | Detects attempts to reach sensitive files, registry keys, processes |
| 5140 | Network share accessed | Identifies unauthorized access to shares |
| 5142 | Network share added | An unauthorized share can exfiltrate data or spread malware |
| 5145 | Share access check | Frequent checks suggest enumeration of shares for later exploitation |
| 5157 | Connection blocked by the Windows Filtering Platform | Helps identify malicious traffic |
| 7045 | New service installed | Much malware installs itself as a service. Stored in the **System** log |

### System Log

| ID | Event | Why it matters |
| -- | ----- | -------------- |
| 1074 | System shutdown or restart, with the reason | Unexpected shutdowns can reveal malware or unauthorized access |
| 6005 | Event Log service started | Marks a boot; a starting point for investigating that period, and exposes unauthorized reboots |
| 6006 | Event Log service stopped | Normally a shutdown; abnormal occurrences suggest disruption to cover activity |
| 6013 | Windows uptime in seconds, once a day | Shorter than expected means the system was rebooted |
| 7040 | Service startup type changed | A change on a crucial service suggests tampering |
| 7045 | New service installed | See above |

### Microsoft Defender

| ID | Event | Why it matters |
| -- | ----- | -------------- |
| 1116 | Malware detected | A surge indicates a targeted attack or widespread infection |
| 1118 | Remediation started | |
| 1119 | Remediation succeeded | Threat neutralised |
| 1120 | Remediation failed | Threat is still live, address immediately |
| 5001 | Real-time protection configuration changed | Unauthorized change may be an attempt to disable Defender |

Order of a clean detection: `1116` → `1118` → `1119` (or `1120` on failure).

## Useful PowerShell Queries

```powershell
# Get failed logon events
Get-WinEvent -LogName Security | Where-Object {$_.Id -eq 4625} | Select-Object TimeCreated, Message | Format-List

# Get all logon events in the last 24 hours
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624; StartTime=(Get-Date).AddDays(-1)}

# Search for a specific event ID across all logs
Get-WinEvent -ListLog * | Where-Object {$_.RecordCount -gt 0} | 
  ForEach-Object { Get-WinEvent -LogName $_.LogName -ErrorAction SilentlyContinue | 
    Where-Object {$_.Id -eq 4698} }

# Export the Security log to an .evtx file for offline analysis
wevtutil epl Security C:\security.evtx
```

## Logon Types (Event 4624)

| Logon Type | Description |
| ---------- | ----------- |
| 2 | Interactive (local keyboard) |
| 3 | Network (SMB, net use) |
| 4 | Batch (scheduled task) |
| 5 | Service logon |
| 7 | Unlock workstation |
| 8 | Network cleartext (IIS basic auth) |
| 10 | RemoteInteractive (RDP) |
| 11 | CachedInteractive (offline logon) |
