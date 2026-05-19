---
tags: [basics, tool, database, oracle]
---


> [!info] See also: [[Oracle TNS]] · [[ODAT]]


Oracle's official interactive SQL CLI. Ships with the Oracle Instant Client (SQL*Plus package).

## Connecting

```bash
# Basic form: user/password@host:port/service_name
sqlplus <username>/<password>@<ip>/<service-name>

# Explicit port (default 1521)
sqlplus <username>/<password>@<ip>:1521/<SID>

# Easy-Connect against a TNS-named service
sqlplus scott/tiger@//<ip>:1521/XE

# Connect as SYSDBA (admin)
sqlplus sys/<password>@//<ip>:1521/XE as sysdba
```

## Once Connected

```sql
-- Server / version info
SELECT * FROM v$version;
SELECT banner FROM v$version WHERE ROWNUM = 1;

-- Current session
SHOW USER;
SELECT user FROM dual;

-- List schemas/users
SELECT username FROM all_users;

-- List tables you can read
SELECT owner, table_name FROM all_tables WHERE owner NOT IN ('SYS','SYSTEM','MDSYS');

-- Show columns of a table
DESC schema.table_name;

-- Extract password hashes (SYSDBA required)
SELECT name, password FROM sys.user$;

-- More modern hash view
SELECT name, spare4 FROM sys.user$;
```

## Useful SQL*Plus Commands

```
SET PAGESIZE 0           -- no headers, useful for scripting
SET LINESIZE 200         -- widen output
SET FEEDBACK OFF
SPOOL output.txt         -- redirect output to a file
SPOOL OFF                -- stop redirecting
HOST <command>           -- run a shell command on the local box
EXIT                     -- quit
```

## Run a One-Shot Query

```bash
echo "SELECT name FROM sys.user$;" | sqlplus -S sys/<password>@//<ip>:1521/XE as sysdba
```
