---
tags: [basics, tool, network]
---

[[Basics/Index|← Basics]]

> [!info] See also: [[SMB]] · [[WinRM]]


## Basic Commands 
```bash 
# Base syntax 
netexec <proto> <target-ip> -u <user> -p <password>
netexec winrm 10.128.42.197 -u user.list -p password.list

# List some shares 
netexec smb 10.129.42.197 -u "user" -p "password" --shares
```