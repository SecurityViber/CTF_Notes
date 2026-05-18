---
tags: [basics, protocol, smb]
---

[[Basics/Index|← Basics]]

> [!info] See also: [[NetExec]]




```cmd
# Mount an smb share to windows 
net use n: \\<ip>\Finance

# Mount smb share using username pasword 
net use n: \\<ip>\Finance /user:plaintext Password123

# Using dir you can search for specific files such as for credentials 
# here /s means to search files in a specified directory and all subdirectories; /b use bare format (no heading information or summary)
dir n:\*cred* /s /b

# Finding specific string in text files can be done using findstr 
findstr /s /i cred n:\*.*
```

Besides the CMD you can use PowerShell as well 

```PowerShell
# List files in a directory 
Get-ChildItem \\<ip>\Finance

# Mount an smb share 
New-PSDrive -Name "N" -Root "\\<ip>\Finance" -PSProvider "FileSystem"

# If you would like to do the same but with credentials 
$username = 'plaintext'
$password = 'Password123'
$secpassword = ConvertTo-SecureString $password -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential $username,$secpassword
New-PSDrive -Name "N" -Root "\\<ip>\Finance" -PSProvider "FileSystem" -Credential $cred

# Searching for specific Strings can be done the following 
Get-ChildItem -Recurse -Path N:\ -Include *cred* -File

# Using "Select-String" is similar to grep
Get-ChildItem -Recurse -Path N:\ | Select-String "cred" -List


```
## Commonly used configs 

```sh 
# SMB Config 
/etc/samba/smb.conf

# Listing the samba shares -> -N is for a Null Session (anonymous)
smbclient -N -L //<ip> 

# SMB client also using an username 
smbclient -u bob \\\\ip\\users 

# Checking current status of smb
smbstatus

# Connect to a specific share
smbclient //<ip>/share

# Connect to a share from CMD
net use n: \\192.168.220.129\Finance /user:plaintext Password123

# SMB and nmap
nmap --script smb-os-discovery.nse -p445 <ip>

# Conect to a share using Powershell 
New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem"

# Mounting a drive in linux - required linux package for that cifs-utils
mount -t cifs //192.168.220.129/Finances /mnt/Finance -o credentials=/path/credentialFile


# Interactive session with SMB using rpcclient
rpcclient -U "" <ip>

# Once connected with rpcclient you can use the following commands (or just fetch the man page)
srvinfo
enumdomains
querydominfo
netshareenumall
enumdomusers
queryuser 0x3e9
```

## Tools to interact with the service 

- smbclient
- CrackMapExec
- SMBMap
- Impacket
- psexec.py
- smbexec.py

## Well known vulnerabilities 

- Eternal Blue 