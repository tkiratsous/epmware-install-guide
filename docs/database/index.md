# Database Installation

This section covers the complete database installation process for EPMware, including Oracle database setup and EPMware schema creation.

## Overview

The EPMware database serves as the central repository for all metadata, workflow configurations, audit trails, and system configurations. Proper database setup is critical for optimal performance and reliability.

## Database Architecture

EPMware uses an Oracle database with the following components:

**Schema Components:**
- **EW Schema** - Main EPMware database schema
- **Data Tablespace (EWD)** - Stores tables and data
- **Index Tablespace (EWX)** - Stores indexes for performance
- **Temporary Space** - For sorting and temporary operations

**Database Objects:**
- Tables for metadata storage
- Indexes for query optimization
- Stored procedures and packages
- Views for reporting
- Sequences for ID generation
- Database jobs for maintenance

## Prerequisites

Before beginning database installation, ensure:

### Oracle Database
- Oracle Database is installed and running
- Version 11.x or later (12c/19c recommended)
- Standard or Enterprise Edition
- Character set: AL32UTF8 recommended

### Access Requirements
- SYS user access to create schemas and grant privileges
- SYSTEM user access for schema creation
- Database connection verified via SQL*Plus or SQL Developer

### Planning Considerations
- Expected data volume and growth rate
- Backup and recovery strategy
- Performance requirements
- High availability needs

## Installation Process Overview

The database installation consists of two main phases:

### Phase 1: Oracle Database Setup
1. Create tablespaces for data and indexes
2. Create the EPMware schema (EW user)
3. Grant necessary privileges
4. Create required functions
5. Create database directories

### Phase 2: Database Objects Installation
1. Extract installation scripts
2. Run the installation utility
3. Verify object creation
4. Configure database parameters

## Pre-Installation Checklist

Before proceeding with installation:

- [ ] Oracle database installed and running
- [ ] SYS user credentials available
- [ ] Sufficient disk space for tablespaces (minimum 2GB)
- [ ] Database backup completed (if existing)
- [ ] Network connectivity verified
- [ ] Installation files downloaded:
  - [ ] ew_db_install.zip
  - [ ] Installation scripts

## Tablespace Planning

Plan your tablespace configuration based on environment size:

### Small Environment
- **EWD (Data):** 1GB initial, 2GB max
- **EWX (Index):** 256MB initial, 512MB max

### Medium Environment
- **EWD (Data):** 2GB initial, 10GB max
- **EWX (Index):** 512MB initial, 2GB max

### Large Environment
- **EWD (Data):** 5GB initial, unlimited max
- **EWX (Index):** 1GB initial, 5GB max

!!! tip "Best Practices"
    - Use Automatic Storage Management (ASM) for production
    - Enable autoextend for tablespaces
    - Monitor tablespace usage regularly
    - Separate data and index tablespaces
    - Consider partitioning for large tables

## Database Parameters

Recommended database initialization parameters:

```sql
-- Memory Configuration
memory_target = 4G          -- Minimum for production
sga_target = 2G
pga_aggregate_target = 1G

-- Process Configuration  
processes = 300
sessions = 400

-- Character Set
nls_character_set = AL32UTF8
nls_nchar_character_set = AL16UTF16

-- Performance
optimizer_mode = ALL_ROWS
db_file_multiblock_read_count = 16
```

## Security Considerations

### Database Security
- Use strong passwords for all database accounts
- Implement password policies
- Enable database auditing
- Restrict network access via sqlnet.ora
- Use SSL/TLS for database connections in production

### Schema Security
- Grant minimum required privileges
- Regularly review granted privileges
- Implement role-based access control
- Monitor failed login attempts

## Backup and Recovery

### Backup Strategy
- Daily full or incremental backups
- Archive log mode enabled
- Test restore procedures regularly
- Offsite backup storage

### Recovery Considerations
- Document recovery procedures
- Define Recovery Time Objective (RTO)
- Define Recovery Point Objective (RPO)
- Regular disaster recovery drills

## Common Database Directories

The following directories are created during installation:

| Directory Name | Purpose | Default Path |
|---------------|---------|--------------|
| archive_db_dir | Archive storage | /ew/db/data/archive |
| stage_db_dir | Staging area | /ew/db/data/stage |
| temp_db_dir | Temporary files | /ew/db/data/temp |

!!! note "Directory Paths"
    Adjust directory paths based on your server's file system structure. Windows paths use backslashes (e.g., D:\ew\db\data\archive).

## Maintenance Tasks

Regular database maintenance ensures optimal performance:

### Daily Tasks
- Monitor alert log
- Check tablespace usage
- Verify backup completion
- Review performance metrics

### Weekly Tasks
- Analyze schema statistics
- Check for invalid objects
- Review slow query logs
- Monitor database growth

### Monthly Tasks
- Perform health check
- Update statistics
- Archive old audit data
- Review and optimize indexes

## Performance Tuning

### Initial Tuning
- Gather schema statistics after installation
- Create appropriate indexes
- Configure connection pooling
- Set appropriate memory parameters

### Ongoing Optimization
- Monitor AWR reports
- Identify and tune slow queries
- Optimize batch processing windows
- Adjust memory allocation as needed

## Troubleshooting Database Issues

Common issues and solutions:

**ORA-01652: Unable to extend temp segment**
- Increase temporary tablespace size
- Check for runaway queries

**ORA-00054: Resource busy**
- Identify locking sessions
- Schedule maintenance during off-hours

**ORA-01555: Snapshot too old**
- Increase undo retention
- Optimize long-running queries

**Connection pool exhausted**
- Increase maximum connections
- Review application connection settings
- Check for connection leaks

## Next Steps

Proceed with database installation:

1. **[Oracle Database Setup](oracle-setup.md)** - Create tablespaces, schema, and grants
2. **[Database Objects Installation](database-objects.md)** - Install EPMware database objects

!!! warning "Critical Steps"
    Do not skip any steps in the database installation process. Each step is required for proper EPMware operation.

## Support

If you encounter issues during database installation:

- Review installation logs
- Check Oracle alert log
- Verify all prerequisites are met
- Contact EPMware Support:
  - Email: support@epmware.com
  - Phone: 408-614-0442

---

© 2025 EPMware, Inc. All rights reserved.
