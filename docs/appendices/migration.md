# Migration Guide

This appendix provides procedures for migrating from previous EPMware versions and upgrading existing installations.

## Version Compatibility Matrix

| Current Version | Target Version | Direct Upgrade | Migration Path |
|----------------|----------------|----------------|----------------|
| 5.x | 6.6 | No | 5.x → 6.0 → 6.6 |
| 6.0 | 6.6 | Yes | Direct upgrade |
| 6.1-6.5 | 6.6 | Yes | Direct upgrade |

## Pre-Migration Checklist

Before starting migration:

- [ ] Review release notes for target version
- [ ] Verify system requirements met
- [ ] Document current configuration
- [ ] Backup database
- [ ] Backup application files
- [ ] Backup configuration files
- [ ] Schedule maintenance window
- [ ] Notify users of downtime
- [ ] Test migration in non-production
- [ ] Prepare rollback plan

## Migration Planning

### Assess Current Environment

```bash
#!/bin/bash
# Environment Assessment Script

echo "EPMware Environment Assessment"
echo "=============================="

# Check current version
echo -n "Current EPMware Version: "
grep "app.version" /opt/epmware/config/application.properties

# Check database version
echo -n "Database Version: "
sqlplus -s ew/password@EPMWARE << EOF
SELECT version FROM ew_system_info;
EXIT;
EOF

# Check disk space
echo "Disk Space Available:"
df -h /opt/epmware

# Count database objects
echo "Database Objects:"
sqlplus -s ew/password@EPMWARE << EOF
SELECT object_type, COUNT(*) 
FROM user_objects 
GROUP BY object_type;
EXIT;
EOF

# Check integrations
echo "Active Integrations:"
ls -la /opt/epmware/config/integrations/
```

### Migration Timeline

| Phase | Duration | Activities |
|-------|----------|------------|
| Planning | 1 week | Assessment, testing, documentation |
| Preparation | 2 days | Backups, staging, communication |
| Migration | 4-8 hours | Database upgrade, application upgrade |
| Validation | 2 hours | Testing, verification |
| Stabilization | 1 week | Monitoring, issue resolution |

## Database Migration

### Backup Current Database

```bash
#!/bin/bash
# Database Backup Script

TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backup/epmware/migration"

# Create backup directory
mkdir -p ${BACKUP_DIR}

# Export database
expdp ew/password@EPMWARE \
      directory=DATA_PUMP \
      dumpfile=ew_pre_migration_${TIMESTAMP}.dmp \
      logfile=ew_pre_migration_${TIMESTAMP}.log \
      full=y

# Backup database files (optional)
rman target / << EOF
BACKUP DATABASE PLUS ARCHIVELOG;
BACKUP CURRENT CONTROLFILE;
EOF

echo "Database backup completed: ${BACKUP_DIR}"
```

### Upgrade Database Schema

```sql
-- Database Schema Upgrade Script
-- Run as EW user

-- Check current version
SELECT * FROM ew_system_info;

-- Run version-specific upgrade scripts
@upgrade_6.0_to_6.1.sql
@upgrade_6.1_to_6.2.sql
@upgrade_6.2_to_6.3.sql
@upgrade_6.3_to_6.4.sql
@upgrade_6.4_to_6.5.sql
@upgrade_6.5_to_6.6.sql

-- Update version
UPDATE ew_system_info 
SET version = '6.6',
    upgrade_date = SYSDATE;

-- Compile invalid objects
EXEC DBMS_UTILITY.compile_schema('EW');

-- Verify upgrade
SELECT COUNT(*) FROM user_objects WHERE status = 'INVALID';
COMMIT;
```

### Data Migration Scripts

```sql
-- Data Migration for New Features

-- Migrate configuration data
INSERT INTO ew_config_new
SELECT config_id, config_name, config_value, 'MIGRATED', SYSDATE
FROM ew_config_old;

-- Update metadata structure
ALTER TABLE ew_metadata ADD (
    new_column1 VARCHAR2(100),
    new_column2 NUMBER,
    migration_flag CHAR(1) DEFAULT 'N'
);

-- Migrate metadata
UPDATE ew_metadata 
SET new_column1 = old_column,
    migration_flag = 'Y'
WHERE migration_flag = 'N';

-- Create new indexes
CREATE INDEX idx_metadata_new ON ew_metadata(new_column1);

-- Drop obsolete objects
DROP TABLE ew_obsolete_table CASCADE CONSTRAINTS;
DROP INDEX idx_obsolete;

COMMIT;
```

## Application Migration

### Backup Current Application

```bash
#!/bin/bash
# Application Backup Script

TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backup/epmware/migration"

# Stop application
systemctl stop tomcat

# Backup application files
tar -czvf ${BACKUP_DIR}/epmware_app_${TIMESTAMP}.tar.gz \
    /opt/tomcat/webapps/epmware/

# Backup configuration
tar -czvf ${BACKUP_DIR}/epmware_config_${TIMESTAMP}.tar.gz \
    /opt/epmware/config/

# Backup Tomcat configuration
tar -czvf ${BACKUP_DIR}/tomcat_config_${TIMESTAMP}.tar.gz \
    /opt/tomcat/conf/

echo "Application backup completed"
```

### Deploy New Application Version

```bash
#!/bin/bash
# Application Deployment Script

# Variables
NEW_WAR="/tmp/epmware-6.6.war"
WEBAPP_DIR="/opt/tomcat/webapps"

# Remove old application
rm -rf ${WEBAPP_DIR}/epmware
rm -f ${WEBAPP_DIR}/epmware.war

# Deploy new version
cp ${NEW_WAR} ${WEBAPP_DIR}/epmware.war

# Start Tomcat
systemctl start tomcat

# Wait for deployment
sleep 60

# Verify deployment
curl -I http://localhost:8080/epmware
```

### Migrate Configuration Files

```bash
#!/bin/bash
# Configuration Migration Script

# Backup existing configuration
cp /opt/tomcat/webapps/epmware/WEB-INF/classes/fs_custom.properties \
   /opt/tomcat/webapps/epmware/WEB-INF/classes/fs_custom.properties.backup

# Merge configuration changes
# Compare and merge manually or use diff tool
diff fs_custom.properties.old fs_custom.properties.new > config_changes.diff

# Apply configuration changes
cat > /opt/tomcat/webapps/epmware/WEB-INF/classes/fs_custom.properties << EOF
# Migrated configuration
# Updated: $(date)

# Existing settings (preserved)
$(grep -v "^#" fs_custom.properties.backup)

# New settings for version 6.6
new.feature.enabled=true
new.feature.config=value
EOF

# Restart application
systemctl restart tomcat
```

## Agent Migration

### Upgrade EPMware Agents

```bash
#!/bin/bash
# Agent Upgrade Script

# Stop agent
systemctl stop epmware-agent

# Backup current agent
cp -r /opt/epmware/agent /opt/epmware/agent.backup

# Extract new agent
cd /opt/epmware
tar -xzvf epmware-agent-6.6.tar.gz

# Preserve configuration
cp /opt/epmware/agent.backup/conf/agent.properties \
   /opt/epmware/agent/conf/

# Update agent registration
/opt/epmware/agent/bin/agent.sh upgrade

# Start agent
systemctl start epmware-agent

# Verify agent status
/opt/epmware/agent/bin/agent.sh status
```

## Post-Migration Tasks

### Validation Checklist

- [ ] Application accessible
- [ ] User login successful
- [ ] Database connectivity verified
- [ ] Agent connectivity confirmed
- [ ] Target applications connected
- [ ] Email notifications working
- [ ] Scheduled jobs running
- [ ] Workflows functioning
- [ ] Reports generating
- [ ] Audit trail recording

### Performance Validation

```bash
#!/bin/bash
# Performance Validation Script

echo "Post-Migration Performance Check"
echo "================================"

# Response time test
time curl -s http://localhost:8080/epmware/login > /dev/null

# Database performance
sqlplus -s ew/password@EPMWARE << EOF
SET TIMING ON
SELECT COUNT(*) FROM ew_metadata;
SELECT COUNT(*) FROM ew_audit;
EXIT;
EOF

# Memory usage
free -h

# Disk I/O
iostat -x 1 5

# Application metrics
curl http://localhost:8080/epmware/metrics
```

### Update Documentation

Update the following documentation:

1. System architecture diagrams
2. Configuration documentation
3. Operational procedures
4. Disaster recovery plans
5. User guides
6. API documentation

## Rollback Procedures

### Database Rollback

```bash
#!/bin/bash
# Database Rollback Script

# Stop application
systemctl stop tomcat

# Restore database
impdp ew/password@EPMWARE \
      directory=DATA_PUMP \
      dumpfile=ew_pre_migration_backup.dmp \
      full=y \
      table_exists_action=replace

# Verify restoration
sqlplus ew/password@EPMWARE << EOF
SELECT * FROM ew_system_info;
EXIT;
EOF
```

### Application Rollback

```bash
#!/bin/bash
# Application Rollback Script

# Stop Tomcat
systemctl stop tomcat

# Remove new version
rm -rf /opt/tomcat/webapps/epmware
rm -f /opt/tomcat/webapps/epmware.war

# Restore previous version
tar -xzvf /backup/epmware/epmware_app_backup.tar.gz -C /

# Restore configuration
tar -xzvf /backup/epmware/epmware_config_backup.tar.gz -C /

# Start Tomcat
systemctl start tomcat

# Verify rollback
curl -I http://localhost:8080/epmware
```

## Troubleshooting Migration Issues

### Common Migration Problems

**Database Upgrade Fails**
```sql
-- Check for errors
SELECT * FROM ew_migration_log WHERE status = 'ERROR';

-- Manually fix issues
-- Rerun failed scripts
```

**Application Won't Start**
```bash
# Check logs
tail -f /opt/tomcat/logs/catalina.out

# Verify configuration
grep ERROR /opt/tomcat/logs/epmware.log
```

**Agent Connection Lost**
```bash
# Re-register agent
/opt/epmware/agent/bin/agent.sh register --key=NEW_KEY

# Check connectivity
telnet epmware.company.com 8443
```

## Migration Best Practices

1. **Test in Non-Production**
   - Complete full migration in test
   - Document all issues
   - Refine procedures

2. **Incremental Approach**
   - Migrate in phases if possible
   - Validate each phase
   - Maintain partial rollback capability

3. **Communication**
   - Notify users well in advance
   - Provide migration schedule
   - Set expectations

4. **Documentation**
   - Document all changes
   - Update runbooks
   - Record lessons learned

5. **Post-Migration Support**
   - Plan for increased support
   - Monitor closely
   - Address issues quickly

## Version-Specific Notes

### Migrating from 5.x to 6.x

Major changes requiring attention:

- New authentication mechanism
- Database schema restructuring
- Configuration file format changes
- API endpoint modifications
- UI framework upgrade

### Migrating from 6.0 to 6.6

Key considerations:

- Enhanced security features
- Performance optimizations
- New workflow engine
- Updated agent architecture
- Cloud integration improvements

## Support

For migration assistance:

- **EPMware Support**: support@epmware.com
- **Phone**: 408-614-0442
- **Documentation**: docs.epmware.com/migration

---

© 2025 EPMware, Inc. All rights reserved.
