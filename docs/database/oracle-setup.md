# Oracle Database Setup

This section covers the Oracle database configuration steps required for EPMware, including tablespace creation, schema setup, and privilege grants.

## Create Tablespaces

Create DATA and INDEX tablespaces for EPMware database objects. These tablespaces will store tables, indexes, and other database objects.

!!! warning "Important"
    Consult your Database Administrator for appropriate location, size, and naming conventions for data files associated with tablespaces.

### Execute as SYS User

Connect to the database as SYS user and execute the following statements:

#### Create Data Tablespace (EWD)

```sql
CREATE TABLESPACE EWD 
DATAFILE '/u01/app/oracle/oradata/epm/EWD.dbf' 
SIZE 1024M 
AUTOEXTEND ON 
NEXT 500K 
MAXSIZE 2048M;
```

For Windows environments:
```sql
CREATE TABLESPACE EWD 
DATAFILE 'D:\app\oracle\oradata\epm\EWD.dbf' 
SIZE 1024M 
AUTOEXTEND ON 
NEXT 500K 
MAXSIZE 2048M;
```

#### Create Index Tablespace (EWX)

```sql
CREATE TABLESPACE EWX 
DATAFILE '/u01/app/oracle/oradata/epm/EWX.dbf' 
SIZE 10M 
AUTOEXTEND ON 
NEXT 500K 
MAXSIZE 512M;
```

For Windows environments:
```sql
CREATE TABLESPACE EWX 
DATAFILE 'D:\app\oracle\oradata\epm\EWX.dbf' 
SIZE 10M 
AUTOEXTEND ON 
NEXT 500K 
MAXSIZE 512M;
```

### Tablespace Sizing Guidelines

| Environment | EWD Initial | EWD Max | EWX Initial | EWX Max |
|------------|-------------|---------|-------------|---------|
| Development | 512M | 2GB | 10M | 256M |
| Test | 1GB | 5GB | 50M | 512M |
| Production | 2GB | Unlimited | 100M | 2GB |

## Create Schema

### Create New User

Login as SYSTEM user and create the EPMware schema user:

```sql
CREATE USER ew IDENTIFIED BY <PASSWORD>;
```

!!! tip "Password Requirements"
    - Minimum 8 characters
    - Include uppercase and lowercase letters
    - Include numbers
    - Include special characters
    - Avoid dictionary words
    - Document in secure location

### Grant Tablespace Quota

Execute the following statements as SYS user to grant disk space quota:

```sql
-- Grant unlimited quota on tablespaces
ALTER USER ew QUOTA UNLIMITED ON EWD;
ALTER USER ew QUOTA UNLIMITED ON EWX;

-- Set default tablespace
ALTER USER ew DEFAULT TABLESPACE EWD;
```

Alternatively, for specific quota limits:
```sql
-- Grant specific quota (example: 5GB on EWD, 1GB on EWX)
ALTER USER ew QUOTA 5G ON EWD;
ALTER USER ew QUOTA 1G ON EWX;
```

## Grant Privileges

### Basic Privileges

Execute these statements as SYS user to grant necessary privileges:

```sql
-- Connection and basic object privileges
GRANT CONNECT TO ew;
GRANT CREATE TABLE TO ew;
GRANT CREATE SEQUENCE TO ew;
GRANT CREATE VIEW TO ew;
GRANT CREATE PROCEDURE TO ew;
GRANT CREATE TYPE TO ew;
GRANT CREATE JOB TO ew;

-- Required for encryption functionality
GRANT EXECUTE ON DBMS_CRYPTO TO ew;
```

### Additional Privileges for Advanced Features

```sql
-- For scheduling and job management
GRANT CREATE ANY JOB TO ew;
GRANT MANAGE SCHEDULER TO ew;

-- For advanced monitoring
GRANT SELECT ON V_$SESSION TO ew;
GRANT SELECT ON V_$PROCESS TO ew;
```

## Create Sleep Function

EPMware requires a sleep function for certain operations. Create this function under the SYS schema:

### Create Function

Execute as SYS user:

```sql
-- Create sleep function wrapper
CREATE OR REPLACE PROCEDURE sleep(seconds NUMBER)
AS
BEGIN
    DBMS_LOCK.SLEEP(seconds);
END;
/

-- Create public synonym
CREATE OR REPLACE PUBLIC SYNONYM sleep FOR sleep;

-- Grant execute permission to EPMware schema
GRANT EXECUTE ON sleep TO ew;
```

### Verify Function Creation

Test the sleep function:

```sql
-- Connect as EW user
CONNECT ew/<password>

-- Test sleep function (should pause for 2 seconds)
BEGIN
    sleep(2);
END;
/
```

## Create Database Directories

Database directories are required for file operations. Execute as SYS user:

### Create Directories

For Linux/Unix environments:
```sql
-- Archive directory
CREATE OR REPLACE DIRECTORY ew_archive_db_dir 
AS '/ew/db/data/archive';

-- Staging directory
CREATE OR REPLACE DIRECTORY ew_stage_db_dir 
AS '/ew/db/data/stage';

-- Temporary directory
CREATE OR REPLACE DIRECTORY ew_temp_db_dir 
AS '/ew/db/data/temp';
```

For Windows environments:
```sql
-- Archive directory
CREATE OR REPLACE DIRECTORY ew_archive_db_dir 
AS 'D:\ew\db\data\archive';

-- Staging directory
CREATE OR REPLACE DIRECTORY ew_stage_db_dir 
AS 'D:\ew\db\data\stage';

-- Temporary directory
CREATE OR REPLACE DIRECTORY ew_temp_db_dir 
AS 'D:\ew\db\data\temp';
```

### Grant Directory Permissions

```sql
-- Grant read/write permissions
GRANT READ, WRITE ON DIRECTORY ew_archive_db_dir TO ew;
GRANT READ, WRITE ON DIRECTORY ew_stage_db_dir TO ew;
GRANT READ, WRITE ON DIRECTORY ew_temp_db_dir TO ew;
```

### Create Physical Directories

Ensure the physical directories exist on the file system:

For Linux/Unix:
```bash
# As root or oracle user
mkdir -p /ew/db/data/archive
mkdir -p /ew/db/data/stage
mkdir -p /ew/db/data/temp

# Set appropriate permissions
chown oracle:oinstall /ew/db/data/*
chmod 755 /ew/db/data/*
```

For Windows:
```batch
:: Create directories
mkdir D:\ew\db\data\archive
mkdir D:\ew\db\data\stage
mkdir D:\ew\db\data\temp
```

## Verify Setup

### Check Tablespaces

```sql
-- Verify tablespace creation
SELECT tablespace_name, 
       bytes/1024/1024 AS size_mb,
       maxbytes/1024/1024 AS max_size_mb,
       autoextensible
FROM dba_data_files 
WHERE tablespace_name IN ('EWD', 'EWX');
```

### Check User and Privileges

```sql
-- Verify user creation
SELECT username, 
       default_tablespace, 
       account_status
FROM dba_users 
WHERE username = 'EW';

-- Check granted privileges
SELECT privilege 
FROM dba_sys_privs 
WHERE grantee = 'EW'
ORDER BY privilege;

-- Check tablespace quotas
SELECT tablespace_name, 
       bytes/1024/1024 AS quota_mb,
       max_bytes/1024/1024 AS max_quota_mb
FROM dba_ts_quotas 
WHERE username = 'EW';
```

### Check Directories

```sql
-- Verify directory creation
SELECT directory_name, 
       directory_path
FROM dba_directories 
WHERE directory_name LIKE 'EW%';

-- Check directory privileges
SELECT privilege, 
       table_name AS directory_name
FROM dba_tab_privs 
WHERE grantee = 'EW' 
  AND table_name LIKE 'EW%DIR';
```

## Post-Setup Tasks

### Gather Statistics

After creating the schema, gather initial statistics:

```sql
-- As SYS user
BEGIN
    DBMS_STATS.GATHER_SCHEMA_STATS(
        ownname => 'EW',
        cascade => TRUE
    );
END;
/
```

### Configure Audit

Enable auditing for security compliance:

```sql
-- Enable auditing for EW schema
AUDIT ALL BY ew BY ACCESS;
AUDIT SELECT TABLE, UPDATE TABLE, INSERT TABLE, DELETE TABLE BY ew BY ACCESS;
```

### Set Resource Limits (Optional)

Create a profile for resource management:

```sql
-- Create profile for EPMware users
CREATE PROFILE epmware_profile LIMIT
    SESSIONS_PER_USER 50
    CPU_PER_SESSION UNLIMITED
    CPU_PER_CALL UNLIMITED
    CONNECT_TIME UNLIMITED
    IDLE_TIME 60
    LOGICAL_READS_PER_SESSION UNLIMITED
    LOGICAL_READS_PER_CALL UNLIMITED
    PRIVATE_SGA UNLIMITED
    FAILED_LOGIN_ATTEMPTS 5
    PASSWORD_LIFE_TIME 90
    PASSWORD_REUSE_TIME 365
    PASSWORD_REUSE_MAX 3
    PASSWORD_LOCK_TIME 1
    PASSWORD_GRACE_TIME 7;

-- Assign profile to EW user
ALTER USER ew PROFILE epmware_profile;
```

## Troubleshooting

### Common Issues

**ORA-01658: Unable to create INITIAL extent**
- Check available disk space
- Verify datafile location permissions
- Ensure filesystem is not full

**ORA-01031: Insufficient privileges**
- Verify connected as SYS user
- Check SYSDBA privilege
- Grant missing privileges

**ORA-01920: User name conflicts with another user or role name**
- Check if user already exists
- Drop existing user if needed: `DROP USER ew CASCADE;`

**Directory creation fails**
- Verify physical path exists
- Check Oracle process has write permissions
- Ensure path syntax is correct for OS

### Validation Script

Run this comprehensive validation script to ensure setup is complete:

```sql
-- Complete validation script
SET LINESIZE 200
SET PAGESIZE 100

PROMPT ========================================
PROMPT EPMware Database Setup Validation
PROMPT ========================================

PROMPT
PROMPT Checking Tablespaces...
SELECT tablespace_name, status, contents 
FROM dba_tablespaces 
WHERE tablespace_name IN ('EWD', 'EWX');

PROMPT
PROMPT Checking Schema...
SELECT username, account_status, default_tablespace 
FROM dba_users 
WHERE username = 'EW';

PROMPT
PROMPT Checking Privileges...
SELECT COUNT(*) AS privilege_count 
FROM dba_sys_privs 
WHERE grantee = 'EW';

PROMPT
PROMPT Checking Directories...
SELECT COUNT(*) AS directory_count 
FROM dba_directories 
WHERE directory_name LIKE 'EW%';

PROMPT
PROMPT Checking Sleep Function...
SELECT object_name, status 
FROM dba_objects 
WHERE object_name = 'SLEEP' 
  AND owner = 'SYS';

PROMPT
PROMPT ========================================
PROMPT Validation Complete
PROMPT ========================================
```

## Next Steps

After completing the Oracle database setup:

1. Proceed to [Database Objects Installation](database-objects.md)
2. Document all passwords securely
3. Backup the database after setup
4. Test connectivity from application server

!!! success "Setup Complete"
    Once all steps are completed successfully, the Oracle database is ready for EPMware database objects installation.

---

© 2025 EPMware, Inc. All rights reserved.
