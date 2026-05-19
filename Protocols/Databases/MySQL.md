---
tags: [basics, protocol, database, mysql]
---


> [!info] See also: [[SQLMap]] · [[SQL Injection]]


## Overview

- Default port: **3306**
- Key system databases:
    - `information_schema` — metadata about all databases, tables, columns, privileges
    - `sys` — human-readable views on `performance_schema` data
    - `mysql` — user accounts, privileges, plugin config
    - `performance_schema` — runtime performance diagnostics

---
## Tools

|Tool|Use Case|
|---|---|
|`mysql` CLI|Primary CLI client (Linux/Windows)|
|[DBeaver](https://dbeaver.io/)|Cross-platform GUI, good for visualising schemas|
|[MySQL Workbench](https://www.mysql.com/products/workbench/)|Official GUI, ERD support|

---

## Enumeration

### Nmap

```bash
sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*
```

Useful NSE scripts:

- `mysql-info` — server version, capabilities
- `mysql-databases` — lists databases (if anon login allowed)
- `mysql-empty-password` — checks for root with no password
- `mysql-brute` — brute forces credentials

---

## Connecting

```bash
# Linux
mysql -u <user> -p<password> -h <ip>

# With port specified
mysql -u <user> -p<password> -h <ip> -P 3306

# Windows
mysql.exe -u <user> -p<password> -h <ip>

# Suppress password warning (useful in scripts)
mysql -u <user> -p<password> -h <ip> 2>/dev/null

# Execute a single query without interactive shell
mysql -u <user> -p<password> -h <ip> -e "SHOW DATABASES;"
```

> [!tip] Password can be passed inline (`-pPassword`, no space) or prompted (`-p` alone). Inline is noisier in process lists but useful for scripting.

---

## Navigation & Enumeration Queries

### Database & Table Discovery

```sql
-- List all databases
SHOW DATABASES;

-- Switch to a database
USE <database>;

-- List tables in current database
SHOW TABLES;

-- List tables across a specific database without switching
SHOW TABLES FROM <database>;

-- Describe columns of a table (types, nullability, keys)
DESCRIBE <table>;
-- or equivalently:
SHOW COLUMNS FROM <table>;

-- Get full column detail including defaults
SHOW FULL COLUMNS FROM <table>;
```

### Extracting Data

```sql
-- Dump entire table
SELECT * FROM <table>;

-- Filter rows
SELECT * FROM <table> WHERE <column> = 'value';

-- Limit output (useful for large tables)
SELECT * FROM <table> LIMIT 20;

-- Search for a substring
SELECT * FROM <table> WHERE <column> LIKE '%keyword%';

-- Select specific columns
SELECT <col1>, <col2> FROM <table>;
```

### information_schema Queries (Key for Pentesting)

```sql
-- List all databases
SELECT schema_name FROM information_schema.schemata;

-- List all tables across all databases
SELECT table_schema, table_name 
FROM information_schema.tables 
WHERE table_schema NOT IN ('information_schema','performance_schema','sys','mysql');

-- List all columns for a specific table
SELECT column_name, data_type 
FROM information_schema.columns 
WHERE table_name = '<table>';

-- Find columns that might contain credentials
SELECT table_schema, table_name, column_name 
FROM information_schema.columns 
WHERE column_name IN ('password','passwd','pwd','secret','token','hash','api_key');
```

---

## User & Privilege Enumeration

```sql
-- Current user context
SELECT user();
SELECT current_user();

-- List all users
SELECT user, host FROM mysql.user;

-- Check privileges of current user
SHOW GRANTS;

-- Check privileges for a specific user
SHOW GRANTS FOR '<user>'@'<host>';

-- Detailed privilege table
SELECT * FROM mysql.user\G

-- Check if FILE privilege exists (needed for file read/write)
SELECT user, file_priv FROM mysql.user WHERE user = '<user>';
```

> [!warning] `mysql.user` is only readable with sufficient privileges. The `\G` suffix formats output vertically — useful for wide tables.

---

## File Read / Write (Post-Auth)

These require the **FILE** privilege and specific server config (`secure_file_priv`).

```sql
-- Read a local file (e.g. /etc/passwd on Linux)
SELECT LOAD_FILE('/etc/passwd');

-- Write output to a file (useful for webshell dropping if webroot is known)
SELECT 'payload' INTO OUTFILE '/var/www/html/shell.php';

-- Check where file operations are restricted
SHOW VARIABLES LIKE 'secure_file_priv';
-- Empty value = unrestricted, NULL = disabled entirely
```

---

## Version & Config Fingerprinting

```sql
-- Server version
SELECT version();
SELECT @@version;

-- OS and compile info
SELECT @@version_compile_os;

-- Data directory location
SELECT @@datadir;

-- Check plugin directory
SELECT @@plugin_dir;

-- List all global variables (verbose)
SHOW VARIABLES;

-- Filter for interesting variables
SHOW VARIABLES LIKE '%secure%';
SHOW VARIABLES LIKE '%log%';
SHOW VARIABLES LIKE '%general%';
```

---

## Useful Shell One-liners

```bash
# Dump a full table to CSV
mysql -u <user> -p<password> -h <ip> -e \
  "SELECT * FROM <db>.<table>" | sed 's/\t/,/g' > dump.csv

# Execute SQL from a file
mysql -u <user> -p<password> -h <ip> < queries.sql

# Pipe multiple queries inline
mysql -u <user> -p<password> -h <ip> <<EOF
USE <database>;
SHOW TABLES;
SELECT * FROM users LIMIT 5;
EOF
```

---

## References

- [[SQL Injection]] — exploiting MySQL via injection vectors
- [HackTricks - MySQL](https://book.hacktricks.xyz/network-services-pentesting/pentesting-mysql)
- [PayloadsAllTheThings - MySQL Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection#mysql)