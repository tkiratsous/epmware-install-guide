# Post-Installation

This section covers post-installation verification, initial configuration, and validation steps to ensure EPMware is properly installed and operational.

## Overview

After completing the EPMware installation, perform these verification and configuration steps to ensure the system is ready for production use.

## Installation Verification

### Component Checklist

Verify all components are installed and running:

- [ ] Oracle Database running
- [ ] EPMware schema created
- [ ] Tomcat Apache running
- [ ] EPMware application deployed
- [ ] Agents installed and connected
- [ ] Target applications configured
- [ ] Email configuration tested
- [ ] Security settings applied

### Service Status Verification

#### Linux Services

```bash
# Check database
sudo systemctl status oracle

# Check Tomcat
sudo systemctl status tomcat

# Check agent
sudo systemctl status epmware-agent

# Check all EPMware services
systemctl list-units | grep epmware
```

#### Windows Services

```batch
:: Check services
sc query OracleServiceORCL
sc query EPMware
sc query "EPMware Agent"

:: View all services
services.msc
```

## Database Connectivity

### Test Database Connection

```bash
# Test with sqlplus
sqlplus ew/password@EPMWARE

SQL> SELECT COUNT(*) FROM user_tables;
SQL> SELECT * FROM ew_system_params;
SQL> exit;
```

### Verify Database Objects

```sql
-- Check object counts
SELECT object_type, COUNT(*) as count
FROM user_objects
WHERE status = 'VALID'
GROUP BY object_type
ORDER BY object_type;

-- Verify core tables exist
SELECT table_name 
FROM user_tables 
WHERE table_name LIKE 'EW_%'
ORDER BY table_name;
```

## Application Startup

### Verify Application Deployment

```bash
# Check deployment
ls -la /opt/tomcat/webapps/epmware/

# Check logs for errors
grep ERROR /opt/tomcat/logs/catalina.out
tail -f /opt/tomcat/logs/epmware.log
```

### Access Application

1. Open web browser
2. Navigate to: `http://server:8080/epmware`
3. Verify login page appears
4. Check for SSL if configured: `https://server:8443/epmware`

## Initial Login

### Default Credentials

Login with default administrator account:

- Username: `admin`
- Password: `admin123`

!!! warning "Security Alert"
    Change the default password immediately after first login!

### Change Admin Password

1. Login as admin
2. Navigate to **User Profile**
3. Click **Change Password**
4. Enter new password meeting complexity requirements:
   - Minimum 8 characters
   - Uppercase and lowercase letters
   - Numbers and special characters
5. Confirm password change
6. Log out and log back in

## Admin Setup

### Create Additional Administrators

1. Navigate to **Configuration → Users**
2. Click **Add User**
3. Enter user details:
   - Username
   - Email address
   - Full name
   - Role: Administrator
4. Set temporary password
5. Save user

### Configure Admin Email

1. Navigate to **Configuration → Misc → Global**
2. Set Admin Email Address
3. Configure email alerts
4. Save settings

## First-Time Configuration

### System Parameters

Configure essential system parameters:

1. Navigate to **Configuration → System**
2. Set parameters:
   - System Name
   - Environment (DEV/TEST/PROD)
   - Base URL
   - Timezone
   - Date format
3. Save configuration

### License Installation

If license required:

1. Navigate to **Configuration → License**
2. Click **Import License**
3. Select license file
4. Verify license details
5. Apply license

## Agent Communication

### Verify Agent Connectivity

1. Navigate to **Configuration → Agents**
2. Check agent status:
   - Green: Connected
   - Red: Disconnected
   - Yellow: Warning
3. View agent details
4. Test agent communication

### Test Agent Deployment

Create test deployment:

1. Navigate to **Deployment → Test**
2. Select agent
3. Create simple test task
4. Execute deployment
5. Verify successful completion

## Test Deployment

### Create Test Metadata

1. Navigate to **Metadata → Dimensions**
2. Create test dimension
3. Add test members
4. Save metadata

### Deploy Test Metadata

1. Navigate to **Deployment → Deploy**
2. Select test metadata
3. Choose target application
4. Execute deployment
5. Verify in target application

## Performance Baseline

### Establish Performance Metrics

Document initial performance metrics:

```bash
# System resources
free -h
df -h
top -b -n 1

# Database performance
sqlplus ew/password@EPMWARE
SQL> SELECT * FROM v$sysstat WHERE name LIKE '%physical%';

# Application metrics
curl http://localhost:8080/epmware/metrics
```

### Create Performance Report

| Metric | Value | Acceptable Range |
|--------|-------|-----------------|
| CPU Usage | 15% | < 70% |
| Memory Usage | 4GB | < 80% available |
| Disk Usage | 20GB | < 80% capacity |
| Database Connections | 10 | < 40 |
| Response Time | 200ms | < 1000ms |

## Backup Configuration

### Initial Backup

Create initial system backup:

```bash
# Database backup
expdp ew/password@EPMWARE directory=DATA_PUMP dumpfile=ew_initial.dmp

# Application backup
tar -czvf epmware_initial.tar.gz /opt/tomcat/webapps/epmware/

# Configuration backup
tar -czvf epmware_config.tar.gz /opt/epmware/config/
```

### Configure Automated Backups

1. Navigate to **Configuration → Backup**
2. Enable automated backup
3. Set backup schedule
4. Configure retention policy
5. Test backup process

## Security Hardening

### Apply Security Settings

1. **Disable Unnecessary Ports**
   ```bash
   # Check open ports
   netstat -tuln
   
   # Configure firewall
   sudo firewall-cmd --permanent --add-port=8080/tcp
   sudo firewall-cmd --reload
   ```

2. **Enable SSL/HTTPS**
   - Configure SSL certificate
   - Force HTTPS redirect
   - Update application URLs

3. **Configure Session Security**
   - Set session timeout
   - Enable secure cookies
   - Configure CSRF protection

### Security Audit

Perform initial security audit:

- [ ] Default passwords changed
- [ ] Unnecessary services disabled
- [ ] Firewall rules configured
- [ ] SSL certificates valid
- [ ] Audit logging enabled
- [ ] User permissions reviewed

## Integration Testing

### Test Email Integration

1. Navigate to **Configuration → Email Test**
2. Send test email
3. Verify receipt
4. Check spam folders

### Test LDAP/AD Integration

If configured:

1. Navigate to **Configuration → LDAP Test**
2. Enter test username
3. Verify authentication
4. Check group membership

### Test Target Applications

For each target application:

1. Test connection
2. Import metadata
3. Deploy test changes
4. Verify in target system

## Documentation

### Document Configuration

Create documentation of:

- System architecture diagram
- Network topology
- Server specifications
- Database configuration
- Application settings
- Integration points
- Service accounts
- Backup procedures

### Create Runbook

Document operational procedures:

1. Startup procedures
2. Shutdown procedures
3. Backup and recovery
4. Monitoring checks
5. Troubleshooting steps
6. Escalation contacts

## User Training

### Administrator Training

Train administrators on:

- System administration
- User management
- Security configuration
- Backup and recovery
- Monitoring and alerts
- Troubleshooting

### End User Training

Train end users on:

- Login procedures
- Navigation
- Metadata requests
- Workflow approval
- Reports and queries

## Monitoring Setup

### Configure Monitoring

Set up monitoring for:

```properties
# System Monitoring
monitor.cpu.enabled=true
monitor.memory.enabled=true
monitor.disk.enabled=true
monitor.network.enabled=true

# Application Monitoring
monitor.response.time=true
monitor.error.rate=true
monitor.user.sessions=true
monitor.database.connections=true
```

### Set Alert Thresholds

Configure alert thresholds:

| Metric | Warning | Critical |
|--------|---------|----------|
| CPU Usage | 70% | 90% |
| Memory Usage | 80% | 95% |
| Disk Usage | 70% | 85% |
| Response Time | 2s | 5s |
| Error Rate | 1% | 5% |

## Performance Tuning

### Initial Tuning

Apply initial performance optimizations:

1. **Database Tuning**
   ```sql
   -- Gather statistics
   EXEC DBMS_STATS.GATHER_SCHEMA_STATS('EW');
   
   -- Check for missing indexes
   SELECT * FROM user_indexes WHERE status = 'UNUSABLE';
   ```

2. **Application Tuning**
   - Adjust heap size
   - Configure connection pools
   - Enable caching

3. **System Tuning**
   - Optimize kernel parameters
   - Configure swap space
   - Adjust file limits

## Validation Checklist

### System Validation

- [ ] All services running
- [ ] Database accessible
- [ ] Application responsive
- [ ] Agents connected
- [ ] Email working
- [ ] LDAP/AD integrated
- [ ] Target applications connected
- [ ] Backups configured
- [ ] Monitoring active
- [ ] Documentation complete

### Functional Validation

- [ ] User login successful
- [ ] Metadata import working
- [ ] Workflow functioning
- [ ] Deployment successful
- [ ] Reports generating
- [ ] Audit trail recording

## Sign-Off

### Acceptance Criteria

Verify acceptance criteria met:

1. System installed per specifications
2. All components operational
3. Performance acceptable
4. Security configured
5. Documentation complete
6. Training delivered
7. Support contacts established

### Formal Sign-Off

Obtain sign-off from:

- [ ] System Administrator
- [ ] Database Administrator
- [ ] Security Team
- [ ] Business Owner
- [ ] End User Representative

## Transition to Production

### Production Readiness

Ensure production readiness:

1. Change management approved
2. Rollback plan documented
3. Support team trained
4. Monitoring configured
5. Backup verified
6. DR plan tested

### Go-Live Checklist

- [ ] Production backup taken
- [ ] Maintenance window scheduled
- [ ] Stakeholders notified
- [ ] Support team ready
- [ ] Rollback plan ready
- [ ] Success criteria defined

## Support Information

### EPMware Support

- **Email**: support@epmware.com
- **Phone**: 408-614-0442
- **Portal**: support.epmware.com
- **Documentation**: docs.epmware.com

### Internal Support

Document internal support:

- Primary Contact: [Name]
- Backup Contact: [Name]
- Escalation Path: [Define]
- On-call Schedule: [Define]

## Next Steps

After post-installation verification:

1. Schedule regular maintenance
2. Plan capacity review
3. Implement monitoring
4. Document procedures
5. Train additional staff

---

© 2025 EPMware, Inc. All rights reserved.
