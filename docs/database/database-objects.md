# Database Objects Installation

This section covers the installation of EPMware database objects including tables, indexes, stored procedures, and other schema objects.

## Prerequisites

Before installing database objects, ensure:

### Database Setup Complete
- Oracle EPMware schema (EW) created with all necessary grants
- Tablespaces (EWD, EWX) created and accessible
- Sleep function created and tested
- Database directories created and permissions granted

### Software Requirements
- Oracle client installed on installation machine
- Database connectivity verified via SQL*Plus
- TNS entry configured in tnsnames.ora
- Jython installed and in system PATH

### Installation Files
- **ew_db_install.zip** - Database installation package
- Extract location accessible
- Sufficient disk space for extraction

!!! note "Installation Location"
    The database objects can be installed from any machine that has Oracle client connectivity to the database. It doesn't have to be the EPMware application server.

## Extract Installation Files

### Download and Extract

1. Copy **ew_db_install.zip** to the installation server
2. Extract the contents:

For Linux/Unix:
```bash
# Create installation directory
mkdir -p /opt/epmware/db_install
cd /opt/epmware/db_install

# Extract files
unzip ew_db_install.zip

# Verify extraction
ls -la
```

For Windows:
```batch
:: Create installation directory
mkdir D:\epmware\db_install
cd D:\epmware\db_install

:: Extract files (use your preferred extraction tool)
:: Right-click and "Extract All" or use 7-Zip/WinZip

:: Verify extraction
dir
```

### Installation Package Contents

The extracted package should contain:

| File/Folder | Description |
|------------|-------------|
| ew_install.bat | Windows installation script |
| ew_install.sh | Unix/Linux installation script |
| scripts/ | SQL scripts directory |
| config/ | Configuration files |
| logs/ | Installation logs directory |
| README.txt | Installation notes |

## Configure Database Connection

### Update TNS Entry

Ensure the database connection is configured in tnsnames.ora:

```sql
EPMWARE =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = dbserver.company.com)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = epm)
    )
  )
```

### Test Connection

Verify database connectivity:

```bash
sqlplus ew/<password>@EPMWARE

SQL> SELECT sysdate FROM dual;
SQL> exit;
```

## Run Installation Script

The installation script automates the creation of all database objects.

### Installation Parameters

The script accepts the following parameters:

| Parameter | Description | Required | Default |
|-----------|-------------|----------|---------|
| Schema Name | EPMware schema name (e.g., EW) | Yes | - |
| Schema Password | Password for schema | Yes | - |
| Database SID | TNS entry name | Yes | - |
| Delete Objects | Delete existing objects (Y/N) | No | N |
| Resume | Resume from previous failure (Y/N) | No | N |

### Execute Installation

#### For Windows

Run as Administrator:
```batch
cd D:\epmware\db_install
ew_install.bat

:: You will be prompted for parameters:
:: Enter Schema Name: EW
:: Enter Schema Password: ********
:: Enter Database SID (tnsnames entry): EPMWARE
:: Delete existing objects? (Y/N) [N]: N
:: Resume from previous installation? (Y/N) [N]: N
```

Or provide parameters directly:
```batch
ew_install.bat EW <password> EPMWARE N N
```

#### For Linux/Unix

```bash
cd /opt/epmware/db_install
chmod +x ew_install.sh
./ew_install.sh

# You will be prompted for parameters:
# Enter Schema Name: EW
# Enter Schema Password: ********
# Enter Database SID (tnsnames entry): EPMWARE
# Delete existing objects? (Y/N) [N]: N
# Resume from previous installation? (Y/N) [N]: N
```

Or provide parameters directly:
```bash
./ew_install.sh EW <password> EPMWARE N N
```

### Installation Output

Expected output during successful installation:

```
========================================
EPMware Database Installation
========================================
Schema Name: EW
Database SID: EPMWARE
Delete Objects: N
Resume Installation: N
========================================

Connecting to database...
Connection successful!

Installing database objects...
[INFO] Creating tables... (50 objects)
[INFO] Creating indexes... (75 objects)
[INFO] Creating sequences... (25 objects)
[INFO] Creating views... (15 objects)
[INFO] Creating procedures... (30 objects)
[INFO] Creating packages... (20 objects)
[INFO] Creating triggers... (10 objects)
[INFO] Creating jobs... (5 objects)
[INFO] Loading initial data...
[INFO] Compiling invalid objects...

========================================
Installation completed successfully!
Total objects created: 230
Invalid objects: 0
Installation time: 3 minutes 24 seconds
========================================

Please check ew_driver.log for details.
```

## Verify Installation

### Check Installation Log

Review the installation log for any errors:

```bash
# View the log file
cat ew_driver.log

# Check for errors
grep -i error ew_driver.log
grep -i warning ew_driver.log
```

### Validate Database Objects

Connect to the database and verify object creation:

```sql
-- Connect as EW user
sqlplus ew/<password>@EPMWARE

-- Check object counts
SELECT object_type, COUNT(*) as count
FROM user_objects
WHERE status = 'VALID'
GROUP BY object_type
ORDER BY object_type;
```

Expected object counts (approximate):

| Object Type | Minimum Count |
|------------|---------------|
| TABLE | 45+ |
| INDEX | 70+ |
| SEQUENCE | 20+ |
| VIEW | 10+ |
| PROCEDURE | 25+ |
| PACKAGE | 15+ |
| PACKAGE BODY | 15+ |
| TRIGGER | 8+ |

### Check for Invalid Objects

```sql
-- Check for invalid objects
SELECT object_type, object_name
FROM user_objects
WHERE status = 'INVALID'
ORDER BY object_type, object_name;

-- If invalid objects exist, compile them
BEGIN
    DBMS_UTILITY.compile_schema(
        schema => 'EW',
        compile_all => TRUE
    );
END;
/
```

### Verify Core Tables

Ensure critical tables are created:

```sql
-- Check core tables
SELECT table_name 
FROM user_tables 
WHERE table_name IN (
    'EW_USERS',
    'EW_DIMENSIONS',
    'EW_WORKFLOWS',
    'EW_REQUESTS',
    'EW_AUDIT',
    'EW_APPLICATIONS',
    'EW_CONFIGURATIONS'
)
ORDER BY table_name;
```

### Verify Initial Data

Check that initial configuration data is loaded:

```sql
-- Check configuration data
SELECT COUNT(*) AS config_count 
FROM ew_configurations;

-- Check default users
SELECT username, status 
FROM ew_users 
WHERE username IN ('ADMIN', 'SYSTEM');

-- Check system parameters
SELECT param_name, param_value 
FROM ew_system_params 
WHERE param_name LIKE 'SYSTEM_%';
```

## Post-Installation Tasks

### Gather Statistics

Update database statistics for optimal performance:

```sql
-- Gather schema statistics
BEGIN
    DBMS_STATS.GATHER_SCHEMA_STATS(
        ownname => 'EW',
        cascade => TRUE,
        estimate_percent => DBMS_STATS.AUTO_SAMPLE_SIZE,
        method_opt => 'FOR ALL COLUMNS SIZE AUTO'
    );
END;
/
```

### Create Database Backup

Backup the database after successful installation:

```bash
# Using RMAN
rman target /

RMAN> BACKUP DATABASE PLUS ARCHIVELOG;
```

### Configure Scheduled Jobs

Verify scheduled jobs are created and enabled:

```sql
-- Check scheduled jobs
SELECT job_name, enabled, state
FROM user_scheduler_jobs
ORDER BY job_name;

-- Enable all jobs if needed
BEGIN
    FOR job IN (SELECT job_name FROM user_scheduler_jobs) LOOP
        DBMS_SCHEDULER.ENABLE(job.job_name);
    END LOOP;
END;
/
```

### Set Up Monitoring

Create monitoring views for ongoing maintenance:

```sql
-- Create monitoring view for large tables
CREATE OR REPLACE VIEW v_table_sizes AS
SELECT 
    segment_name AS table_name,
    ROUND(bytes/1024/1024, 2) AS size_mb,
    tablespace_name
FROM user_segments
WHERE segment_type = 'TABLE'
ORDER BY bytes DESC;

-- Create view for index statistics
CREATE OR REPLACE VIEW v_index_stats AS
SELECT 
    index_name,
    table_name,
    uniqueness,
    status
FROM user_indexes
ORDER BY table_name, index_name;
```

## Troubleshooting Installation

### Common Installation Issues

**Error: ORA-01017: Invalid username/password**
- Verify schema password is correct
- Check TNS entry is correct
- Ensure case sensitivity of password

**Error: ORA-12154: TNS could not resolve the connect identifier**
- Verify TNS entry exists in tnsnames.ora
- Check TNS_ADMIN environment variable
- Test with tnsping command

**Error: Objects already exist**
- Run installation with Delete Objects = Y to clean install
- Or manually drop existing objects first

**Error: Insufficient privileges**
- Verify all grants were executed successfully
- Re-run grant statements as SYS user

**Python/Jython errors**
- Verify Jython is installed and in PATH
- Check Jython version compatibility
- Ensure Java is properly configured

### Manual Object Compilation

If automatic compilation fails:

```sql
-- Compile specific object types
ALTER PACKAGE package_name COMPILE;
ALTER PACKAGE package_name COMPILE BODY;
ALTER PROCEDURE procedure_name COMPILE;
ALTER FUNCTION function_name COMPILE;
ALTER TRIGGER trigger_name COMPILE;
ALTER VIEW view_name COMPILE;

-- Compile all invalid objects
EXEC DBMS_UTILITY.compile_schema('EW');
```

### Rollback Installation

If installation needs to be rolled back:

```sql
-- Connect as SYS user
sqlplus sys/<password>@EPMWARE as sysdba

-- Drop user and all objects (cascading)
DROP USER ew CASCADE;

-- Drop tablespaces including contents
DROP TABLESPACE EWD INCLUDING CONTENTS AND DATAFILES;
DROP TABLESPACE EWX INCLUDING CONTENTS AND DATAFILES;

-- Drop directories
DROP DIRECTORY ew_archive_db_dir;
DROP DIRECTORY ew_stage_db_dir;
DROP DIRECTORY ew_temp_db_dir;

-- Then restart installation process from beginning
```

## Performance Optimization

### Initial Performance Tuning

After installation, implement these optimizations:

```sql
-- Create function-based indexes for common searches
CREATE INDEX idx_ew_users_upper_username 
ON ew_users(UPPER(username));

-- Create bitmap indexes for low-cardinality columns
CREATE BITMAP INDEX idx_ew_requests_status 
ON ew_requests(status);

-- Partition large audit tables (Enterprise Edition only)
-- Consult with DBA for partitioning strategy
```

### Connection Pool Configuration

Update connection pool settings for the application:

```sql
-- Check current session limits
SELECT name, value 
FROM v$parameter 
WHERE name IN ('sessions', 'processes');

-- If needed, increase limits (as SYS)
ALTER SYSTEM SET processes=500 SCOPE=SPFILE;
ALTER SYSTEM SET sessions=600 SCOPE=SPFILE;
-- Restart database for changes to take effect
```

## Validation Checklist

Complete this checklist before proceeding:

- [ ] Installation script executed successfully
- [ ] No errors in ew_driver.log
- [ ] All objects created and valid
- [ ] Initial data loaded
- [ ] Statistics gathered
- [ ] Database backed up
- [ ] Scheduled jobs enabled
- [ ] Connection from application server tested
- [ ] Performance baseline established

## Next Steps

After successful database object installation:

1. Proceed to [Application Server Setup](../app-server/)
2. Configure database connection parameters
3. Document installation details:
   - Schema name and password
   - TNS entry configuration
   - Object counts
   - Any customizations made

!!! success "Installation Complete"
    The EPMware database objects are now installed and ready for application connectivity.

## Support

For database installation issues:

- Review installation logs carefully
- Check Oracle alert log for database errors
- Verify all prerequisites are met
- Contact EPMware Support:
  - Email: support@epmware.com
  - Phone: 408-614-0442
  - Provide ew_driver.log file with support request

---

© 2025 EPMware, Inc. All rights reserved.
