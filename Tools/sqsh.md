---
tags: [basics, tool, database, mssql]
---


> [!info] See also: [[MSSQL]] · [[Impacket]] · [[SQLMap]]


Linux CLI for MS SQL Server (uses FreeTDS). A common fallback when `mssqlclient.py` isn't available.

## Connecting

```bash
# Basic
sqsh -S <ip> -U <user> -P <password>

# Specify port (default 1433)
sqsh -S <ip>:1433 -U <user> -P <password>

# Authenticate to a named instance
sqsh -S <ip>:1433 -U <user> -P <password> -D <database>
```

## Interactive Syntax

In sqsh, each statement is followed by `go` on its own line to execute the batch:

```sql
SELECT @@version
go

SELECT name FROM sys.databases
go
```

## Useful Built-in Commands

```
\help                -- list internal commands
\db                  -- show current database
\use <db>            -- switch database
\go <count>          -- run last batch N times
\reset               -- clear query buffer
\quit                -- exit
```

## Common SQL for Enumeration

```sql
-- Check sysadmin privilege
SELECT IS_SRVROLEMEMBER('sysadmin')
go

-- List databases
SELECT name FROM sys.databases
go

-- List tables in current DB
SELECT table_name FROM information_schema.tables
go

-- Enable + use xp_cmdshell (sysadmin required)
EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;
EXEC xp_cmdshell 'whoami';
go
```
