---
tags: [basics, protocol, database, mssql]
---

[[Basics/Index|← Basics]]

> [!info] See also: [[SQLMap]]



```bash

# Conecting to a database
sqsh -S 10.129.20.13 -U username -P Password123

# Windows machine
sqlcmd -S 10.129.20.13 -U username -P Password123

# Here some tips to get information out of the MSSQ Database 


```



```sqsh

```


Tools to use:
- mssql-cli
- SQL Server PowerShell
- HeidiSQL
- SQLPro
- Impacket's mssqlclient.py -> Usually most useful one

Default System Databases
- master
- model
- msdb
- tempdb
- resource

Default Port: 1433

### Footprinting


```bash

# Nmap scanning
sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248

# MSSQL Ping in metasploit 
msf6 auxiliary(scanner/mssql/mssql_ping) > set rhosts ...


# Script to use 
mssqlclient.py <user>@<FQDN/IP> -windows-auth
```
