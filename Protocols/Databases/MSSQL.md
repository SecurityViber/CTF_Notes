---
tags: [basics, protocol, database, mssql]
---


> [!info] See also: [[SQLMap]] · [[Impacket]] · [[sqsh]]



```bash
# Connecting to a database (Linux)
sqsh -S 10.129.20.13 -U username -P Password123

# Windows machine
sqlcmd -S 10.129.20.13 -U username -P Password123
```

## Basic Enumeration

```sql
-- List all databases
SELECT name FROM sys.databases;
GO

-- List tables in the current database
SELECT table_name FROM information_schema.tables WHERE table_type = 'BASE TABLE';
GO

-- List users
SELECT name, type_desc FROM sys.server_principals WHERE type IN ('S','U');
GO

-- Check current user privileges
SELECT IS_SRVROLEMEMBER('sysadmin');
GO

-- Read a local file (requires xp_cmdshell or BULK INSERT)
EXEC xp_cmdshell 'whoami';
GO

-- Enable xp_cmdshell (if disabled)
EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;
GO
```


## Tools to interact with the service 

- [[sqsh]] — Linux CLI client (via FreeTDS)
- `sqlcmd` — Microsoft's official client (Windows / cross-platform)
- mssql-cli — Microsoft's modern Python-based CLI
- [[Impacket]] — `mssqlclient.py` is usually the most useful one
- SQL Server PowerShell — `Invoke-Sqlcmd`
- HeidiSQL — GUI
- SQLPro — GUI


## Default System Databases
- master
- model
- msdb
- tempdb
- resource

Default Port: 1433


## Footprinting


```bash
# Nmap scanning
sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248

# MSSQL Ping in metasploit 
msf6 auxiliary(scanner/mssql/mssql_ping) > set rhosts ...


# Script to use 
mssqlclient.py <user>@<FQDN/IP> -windows-auth
```
