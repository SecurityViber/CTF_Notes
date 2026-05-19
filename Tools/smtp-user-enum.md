---
tags: [basics, tool, smtp, enumeration]
---


> [!info] See also: [[SMTP]] · [[swaks]]


User-enumeration tool against SMTP servers. Probes `VRFY`, `EXPN`, and `RCPT TO` to confirm valid local users.

## Modes

- `VRFY` — directly asks "does this user exist?"
- `EXPN` — expands a mailing list (useful against postfix/sendmail relays)
- `RCPT` — issues `MAIL FROM` + `RCPT TO` and inspects the response code

Modern MTAs usually disable VRFY/EXPN. RCPT is the most reliable in the wild.

## Basic Commands

```bash
# VRFY against a single host using a userlist
smtp-user-enum -M VRFY -U /usr/share/wordlists/metasploit/unix_users.txt -t <ip>

# EXPN mode
smtp-user-enum -M EXPN -U users.txt -t <ip>

# RCPT mode — usually best when VRFY/EXPN are disabled
smtp-user-enum -M RCPT -U users.txt -D target.com -t <ip>

# Specify port (non-standard SMTP)
smtp-user-enum -M RCPT -U users.txt -t <ip> -p 587
```

## Useful Wordlists

- `/usr/share/wordlists/metasploit/unix_users.txt`
- `/usr/share/seclists/Usernames/Names/names.txt`
- `/usr/share/seclists/Usernames/cirt-default-usernames.txt`


## Output Filtering

```bash
# Keep only valid hits
smtp-user-enum -M RCPT -U users.txt -D target.com -t <ip> | grep "exists"
```
