# Oracle Database Installation

This section covers the Oracle database configuration steps required for EPMware, including tablespace creation, schema setup, and privilege grants.

## Prerequisites

- Oracle database is installed and is running
- Oracle database access as SYS user

## Create Tablespaces

Create DATA and INDEX tablespaces for EW database objects. These tablespaces will store tables, indexes, and other database objects.

The following statements are shown as an example. 
**Please consult Database Administrator for location, size and naming convention of data files associated with tablespaces.**

Execute the following statements as SYS user:



```sql

-- EWD 

CREATE TABLESPACE EWD 
DATAFILE 'D:\app\epmadmin\oradata\epm\EWD.dbf' 
SIZE 1024M 
AUTOEXTEND ON 
NEXT 500K 
MAXSIZE 2048M;


-- EWX 

CREATE TABLESPACE EWX 
DATAFILE 'D:\app\oracle\oradata\epm\EWX.dbf' 
SIZE 10M 
AUTOEXTEND ON 
NEXT 500K 
MAXSIZE 512M;
```


## Create Schema

### Create New User

Login as SYSTEM user and create new user EW as shown below:


```sql
CREATE USER ew IDENTIFIED BY <PASSWORD>;
```


### Grant Tablespace Quota to EW Schema

Execute following statements as SYS user to grant unlimited disk space quota to EW
tablespaces. Unlimited option can be replaced with other values as needed:

```sql
-- Grant unlimited quota on tablespaces

ALTER USER ew QUOTA UNLIMITED ON EWD;
ALTER USER ew QUOTA UNLIMITED ON EWX;

-- Set default tablespace

ALTER USER ew DEFAULT TABLESPACE EWD;

```

## Grant Privileges

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


## Create Sleep Function

EPMware requires a sleep function for certain operations. 

Create Sleep function and provide EXECUTE grant on that function to EW Schema



**Execute three statements as mentioned below using SYS user:**


Execute as SYS user:

```sql
-- Create sleep function wrapper under SYS Schema 

CREATE OR REPLACE PROCEDURE sleep(seconds NUMBER)
AS
BEGIN
    DBMS_LOCK.SLEEP(seconds);
END;
/

-- Create public synonym
CREATE OR REPLACE PUBLIC SYNONYM sleep FOR sleep;

-- Grant execute permission to EPMware schema
GRANT EXECUTE ON sleep TO <epmware schema> ;
```


## Create Database Directories

Database directories are required for file operations. Execute as SYS user:

### Create Directories

For Linux/Unix environments:
```sql
-- Archive directory

CREATE OR REPLACE DIRECTORY <ew_schema_name>_archive_db_dir AS
'D:\ew\db\data\archive';

CREATE OR REPLACE DIRECTORY <ew_schema_name>_stage_db_dir AS
'D:\ew\db\data\stage';

CREATE OR REPLACE DIRECTORY <ew_schema_name>_temp_db_dir AS
'D:\ew\db\data\temp';

-- Grant read/write permissions

GRANT READ,WRITE ON DIRECTORY <ew_schema_name>_archive_db_dir TO EW;
GRANT READ,WRITE ON DIRECTORY <ew_schema_name>_stage_db_dir TO EW;
GRANT READ,WRITE ON DIRECTORY <ew_schema_name>_temp_db_dir TO EW;

```

