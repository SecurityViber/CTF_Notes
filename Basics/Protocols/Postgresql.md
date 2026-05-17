

# ## Connection
```bash
PGPASSWORD='pass' psql -h 127.0.0.1 -p 5432 -U postgres -d dbname
```

## Orientation
```sql
\l                        -- list all databases
\c dbname                 -- switch to database
\dt                       -- list tables in current db
\dt *.*                   -- list tables in all schemas
\ds                       -- list sequences
\dv                       -- list views
\du                       -- list users/roles
\dn                       -- list schemas
```

## Table Inspection
```sql
\d tablename              -- describe table (columns, types, constraints)
\d+ tablename             -- verbose (includes storage, comments)
\di                       -- list indexes
```

## Dumping Data
```sql
SELECT * FROM users;
SELECT * FROM users LIMIT 10;
SELECT column1, column2 FROM tablename;
SELECT table_name FROM information_schema.tables WHERE table_schema = 'public';
```

## Finding Juicy Stuff
```sql
-- Search for password-related columns across all tables
SELECT table_name, column_name
FROM information_schema.columns
WHERE column_name ILIKE '%pass%'
   OR column_name ILIKE '%secret%'
   OR column_name ILIKE '%token%'
   OR column_name ILIKE '%hash%';

-- Dump all usernames and passwords from users table
SELECT username, password FROM users;

-- Check current db user privileges
\du
SELECT current_user;
SELECT session_user;
```

## PostgreSQL Version & Config
```sql
SELECT version();
SHOW config_file;
SHOW data_directory;
```

## Output & Export
```sql
\o output.txt             -- redirect output to file
\o                        -- stop redirecting
\x                        -- toggle expanded display (easier to read wide rows)
\timing                   -- toggle query timing
```

## Misc
```sql
\q                        -- quit
\!                        -- drop to shell (if permissions allow)
\! whoami                 -- run shell command inline
\?                        -- help on psql meta-commands
\h SELECT                 -- help on SQL syntax
```