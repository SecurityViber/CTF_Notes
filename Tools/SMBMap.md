---
tags: [basics, tool, smb]
---


> [!info] See also: [[SMB]] · [[smbclient]] · [[NetExec]]


SMB share enumerator focused on listing accessible shares + permissions and running commands across hosts. Repo: https://github.com/ShawnDEvans/smbmap

## Listing Shares & Permissions

```bash
# Null session
smbmap -H <ip>

# With credentials
smbmap -H <ip> -u <user> -p <password>

# Domain creds
smbmap -H <ip> -u <user> -p <password> -d <DOMAIN>

# Pass-the-hash
smbmap -H <ip> -u <user> -p :<NTLM-hash>
```

Output columns show READ / WRITE / NO ACCESS for each share — fast triage before connecting with [[smbclient]].

## Listing Files Recursively

```bash
# Recursive listing of a share
smbmap -H <ip> -u <user> -p <password> -R <share>

# With depth limit and filter for interesting extensions
smbmap -H <ip> -u <user> -p <password> -R <share> --depth 3 -A '\.(txt|ini|conf|xml|ps1|kdbx|bak)$'
```

## Downloading & Uploading

```bash
# Download a file
smbmap -H <ip> -u <user> -p <password> --download '<share>\path\to\file.txt'

# Upload a file
smbmap -H <ip> -u <user> -p <password> --upload local.exe '<share>\remote.exe'
```

## Command Execution (if admin on target)

```bash
# Execute via Windows command exec (uses smbexec-style auth)
smbmap -H <ip> -u <user> -p <password> -x 'whoami'
```

## Spraying Across Hosts

```bash
# Test the same credentials against many hosts
smbmap --host-file targets.txt -u <user> -p <password>
```
