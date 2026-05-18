---
tags: [basics, technique, windows]
---

[[Basics/Index|← Basics]]


## System

| Path | Description |
| ---- | ----------- |
| `%SystemRoot%` | Usually `C:\Windows` |
| `%SystemRoot%\System32` | Core system binaries |
| `%SystemRoot%\System32\config\SAM` | Local user password hashes |
| `%SystemRoot%\System32\config\SYSTEM` | Required to decrypt SAM |
| `%SystemRoot%\System32\drivers\etc\hosts` | Hosts file |
| `%SystemRoot%\System32\winevt\Logs` | Windows Event Logs |
| `%SystemRoot%\Panther\Unattend.xml` | Unattended install file (may contain creds) |
| `%WINDIR%\repair\sam` | Backup SAM (older systems) |

## User Profile

| Path | Description |
| ---- | ----------- |
| `%USERPROFILE%` | Current user home (`C:\Users\username`) |
| `%APPDATA%` | `C:\Users\username\AppData\Roaming` |
| `%LOCALAPPDATA%` | `C:\Users\username\AppData\Local` |
| `%USERPROFILE%\.ssh\` | SSH keys |
| `%USERPROFILE%\Documents` | User documents |
| `%TEMP%` | Temp files |

## Common Config / Credential Locations

| Path | Description |
| ---- | ----------- |
| `C:\inetpub\wwwroot\web.config` | IIS application config (DB creds) |
| `C:\ProgramData\` | Machine-wide app data |
| `%APPDATA%\FileZilla\sitemanager.xml` | FTP saved credentials |
| `C:\Windows\SYSVOL\` | Domain policies (GPP passwords) |
| `C:\Users\All Users\` | Alias for `C:\ProgramData` |

## Scheduled Tasks / Services

| Path | Description |
| ---- | ----------- |
| `C:\Windows\System32\Tasks\` | Scheduled tasks (XML format) |
| `HKLM\SYSTEM\CurrentControlSet\Services\` | Service configs in registry |