# Application Settings

This section covers the global configuration settings for EPMware, including email configuration, security settings, and system parameters.

## Global Settings Overview

Global settings control system-wide behavior and integration points for EPMware. Access these settings through:

**Navigation:** Configuration → Misc → Global

## Email Configuration

### Email Server Settings

Configure SMTP server settings for system notifications and workflow emails:

| Setting | Description | Example | Required |
|---------|-------------|---------|----------|
| **SMTP Host** | Mail server hostname or IP | smtp.company.com | Yes |
| **SMTP Port** | Mail server port | 25, 587, 465 | Yes |
| **SMTP Protocol** | Connection protocol | SMTP, SMTPS | Yes |
| **Enable TLS** | Use TLS encryption | true/false | Recommended |
| **Enable SSL** | Use SSL encryption | true/false | Optional |
| **SMTP Username** | Authentication username | mailuser@company.com | If auth required |
| **SMTP Password** | Authentication password | ******** | If auth required |

### Email Content Settings

Configure email formatting and content options:

| Setting | Value | Comments | Required |
|---------|-------|----------|----------|
| **Admin User Email Address** | admin@company.com | Receives system notifications and error alerts | Yes |
| **From Email Address** | epmware@company.com | Sender address for all system emails | Yes |
| **Reply-To Address** | noreply@company.com | Reply-to address for system emails | Optional |
| **Email Domain Name** | company.com | Default domain for user emails | Optional |
| **Email Subject Prefix** | [EPMWARE-PROD] | Prefix added to all email subjects | Optional |
| **Override Email Address** | test@company.com | Redirect all emails (useful for testing) | Optional |
| **Email Template Path** | /opt/epmware/templates | Location of email templates | Optional |

### Email Templates

Configure email templates for different notification types:

```properties
# Workflow Notifications
workflow.email.request.created=request_created.html
workflow.email.request.approved=request_approved.html
workflow.email.request.rejected=request_rejected.html
workflow.email.request.completed=request_completed.html

# System Notifications
system.email.user.created=user_created.html
system.email.password.reset=password_reset.html
system.email.error.notification=error_notification.html

# Deployment Notifications
deployment.email.started=deployment_started.html
deployment.email.success=deployment_success.html
deployment.email.failure=deployment_failure.html
```

### Test Email Configuration

After configuring email settings:

1. Navigate to Configuration → Misc → Global
2. Click "Test Email Configuration"
3. Enter test recipient address
4. Verify test email received

## Security Settings

### LDAP/Active Directory Integration

Configure LDAP/AD for user authentication:

#### LDAP Connection Settings

```properties
# LDAP Server Configuration
ldap.url=ldap://ldap.company.com:389
ldap.base.dn=DC=company,DC=com
ldap.username=CN=svc_epmware,CN=Users,DC=company,DC=com
ldap.password=encrypted_password
ldap.connection.timeout=5000
ldap.read.timeout=5000

# LDAP Search Configuration
ldap.user.search.base=CN=Users
ldap.user.search.filter=(sAMAccountName={0})
ldap.group.search.base=CN=Groups
ldap.group.search.filter=(member={0})

# LDAP Attribute Mapping
ldap.attribute.username=sAMAccountName
ldap.attribute.firstname=givenName
ldap.attribute.lastname=sn
ldap.attribute.email=mail
ldap.attribute.displayname=displayName
ldap.attribute.memberof=memberOf
```

#### Active Directory Specific Settings

```properties
# AD-Specific Configuration
ad.domain=company.com
ad.url=ldap://dc01.company.com:389
ad.backup.url=ldap://dc02.company.com:389
ad.use.ssl=false
ad.trust.store=/opt/epmware/certs/ad-truststore.jks
ad.trust.store.password=changeit

# AD User Settings
ad.user.dn.pattern=CN={0},CN=Users,DC=company,DC=com
ad.user.attributes=sAMAccountName,givenName,sn,mail,memberOf
ad.nested.groups=true
```

### Password Policy

Configure password requirements:

```properties
# Password Complexity
password.min.length=8
password.max.length=32
password.require.uppercase=true
password.require.lowercase=true
password.require.numbers=true
password.require.special=true
password.special.chars=!@#$%^&*()

# Password History
password.history.count=5
password.history.days=365

# Password Expiration
password.expire.days=90
password.expire.warning.days=14
password.grace.period.days=7

# Account Lockout
password.max.attempts=5
password.lockout.duration=30
password.reset.token.expiry=24
```

### Session Management

Configure session security settings:

```properties
# Session Configuration
session.timeout.minutes=30
session.absolute.timeout.hours=8
session.concurrent.max=1
session.secure.cookie=true
session.http.only=true
session.same.site=strict

# Remember Me
remember.me.enabled=false
remember.me.duration.days=30
remember.me.secure=true
```

## Application Settings

### System Parameters

Core system configuration parameters:

| Parameter | Default | Description |
|-----------|---------|-------------|
| **System Name** | EPMware | Application display name |
| **System URL** | http://localhost:8080/epmware | Base URL for links |
| **System Timezone** | America/New_York | Default timezone |
| **System Locale** | en_US | Default locale |
| **Date Format** | MM/dd/yyyy | Date display format |
| **Time Format** | HH:mm:ss | Time display format |
| **Number Format** | #,##0.00 | Number display format |
| **Currency Symbol** | $ | Default currency symbol |

### File Management Settings

```properties
# File Upload Configuration
file.upload.max.size=104857600
file.upload.allowed.extensions=txt,csv,xml,json,xlsx,xls
file.upload.denied.extensions=exe,bat,sh,jar,war
file.upload.scan.enabled=true
file.upload.quarantine.enabled=true

# File Storage
file.storage.path=/opt/epmware/files
file.storage.archive.path=/opt/epmware/archive
file.storage.temp.path=/opt/epmware/temp
file.storage.retention.days=90
file.storage.archive.days=365

# File Processing
file.processing.batch.size=100
file.processing.thread.count=4
file.processing.timeout.minutes=60
```

### Audit Settings

Configure audit logging:

```properties
# Audit Configuration
audit.enabled=true
audit.log.all.requests=false
audit.log.modifications=true
audit.log.authentication=true
audit.log.authorization=true
audit.log.deployments=true

# Audit Retention
audit.retention.days=365
audit.archive.enabled=true
audit.archive.path=/opt/epmware/audit/archive
audit.compress.archived=true
audit.purge.archived.days=1825
```

## Integration Settings

### External System Integration

Configure integration with external systems:

#### REST API Settings

```properties
# API Configuration
api.enabled=true
api.base.path=/api/v1
api.auth.type=token
api.token.header=Authorization
api.token.prefix=Bearer
api.rate.limit=1000
api.rate.window=3600

# API Security
api.ssl.required=true
api.cors.enabled=true
api.cors.origins=https://app.company.com
api.cors.methods=GET,POST,PUT,DELETE
api.cors.headers=Content-Type,Authorization
```

#### Webhook Configuration

```properties
# Webhook Settings
webhook.enabled=true
webhook.url=https://webhook.company.com/epmware
webhook.auth.type=basic
webhook.auth.username=webhook_user
webhook.auth.password=encrypted_password
webhook.retry.count=3
webhook.retry.delay=5000
webhook.timeout=30000

# Webhook Events
webhook.events.request.created=true
webhook.events.request.approved=true
webhook.events.request.deployed=true
webhook.events.user.created=true
webhook.events.error.critical=true
```

## Notification Settings

### Notification Preferences

Configure system-wide notification settings:

```properties
# Email Notifications
notification.email.enabled=true
notification.email.batch.size=50
notification.email.queue.size=1000
notification.email.thread.count=2

# In-App Notifications
notification.app.enabled=true
notification.app.retention.days=30
notification.app.max.per.user=100

# SMS Notifications (Optional)
notification.sms.enabled=false
notification.sms.provider=twilio
notification.sms.api.key=your_api_key
notification.sms.from.number=+1234567890
```

### Notification Rules

Define when notifications are sent:

| Event | Email | In-App | SMS | Configurable |
|-------|-------|--------|-----|--------------|
| Request Created | Yes | Yes | No | Yes |
| Request Approved | Yes | Yes | No | Yes |
| Request Rejected | Yes | Yes | No | Yes |
| Request Deployed | Yes | Yes | No | Yes |
| Deployment Failed | Yes | Yes | Yes | Yes |
| System Error | Yes | No | Yes | Yes |
| Password Expiry | Yes | Yes | No | Yes |
| Account Locked | Yes | No | Yes | Yes |

## Performance Settings

### Cache Configuration

```properties
# Cache Settings
cache.enabled=true
cache.provider=ehcache
cache.config.file=/opt/epmware/conf/ehcache.xml

# Cache Regions
cache.user.enabled=true
cache.user.ttl=3600
cache.user.max.entries=1000

cache.metadata.enabled=true
cache.metadata.ttl=7200
cache.metadata.max.entries=5000

cache.config.enabled=true
cache.config.ttl=86400
cache.config.max.entries=500
```

### Thread Pool Settings

```properties
# Thread Pool Configuration
thread.pool.core.size=10
thread.pool.max.size=50
thread.pool.queue.capacity=100
thread.pool.keep.alive=60

# Async Processing
async.enabled=true
async.core.pool.size=5
async.max.pool.size=20
async.queue.capacity=50
```

## Maintenance Settings

### Scheduled Maintenance Tasks

Configure automatic maintenance tasks:

```properties
# Database Maintenance
maintenance.db.analyze.enabled=true
maintenance.db.analyze.schedule=0 0 2 * * SUN
maintenance.db.vacuum.enabled=true
maintenance.db.vacuum.schedule=0 0 3 * * SUN

# File Cleanup
maintenance.file.cleanup.enabled=true
maintenance.file.cleanup.schedule=0 0 1 * * *
maintenance.file.cleanup.age.days=30

# Log Rotation
maintenance.log.rotation.enabled=true
maintenance.log.rotation.schedule=0 0 0 * * *
maintenance.log.retention.days=90

# Session Cleanup
maintenance.session.cleanup.enabled=true
maintenance.session.cleanup.schedule=0 */30 * * * *
```

## Backup and Recovery Settings

### Backup Configuration

```properties
# Backup Settings
backup.enabled=true
backup.path=/opt/epmware/backup
backup.schedule=0 0 0 * * *
backup.retention.count=30
backup.compress=true

# Backup Components
backup.database=true
backup.files=true
backup.configurations=true
backup.logs=false

# Remote Backup
backup.remote.enabled=true
backup.remote.type=sftp
backup.remote.host=backup.company.com
backup.remote.path=/backups/epmware
backup.remote.username=backup_user
backup.remote.password=encrypted_password
```

## Monitoring and Alerting

### Health Check Settings

```properties
# Health Check Configuration
health.check.enabled=true
health.check.interval=60
health.check.timeout=10

# Health Check Components
health.check.database=true
health.check.ldap=true
health.check.email=true
health.check.disk.space=true
health.check.memory=true
health.check.target.apps=true

# Alerting Thresholds
alert.disk.space.threshold=80
alert.memory.threshold=90
alert.connection.pool.threshold=80
alert.thread.pool.threshold=90
```

## Applying Settings Changes

### Save and Apply Settings

1. Make configuration changes
2. Click "Save Settings"
3. Some settings require application restart:
   - Database connection settings
   - Thread pool configurations
   - Cache settings
   - Security configurations

### Restart Application

For settings requiring restart:

```bash
# Linux
sudo systemctl restart tomcat

# Windows
net stop EPMware
net start EPMware
```

## Configuration Best Practices

1. **Test in Non-Production First**
   - Apply settings changes in test environment
   - Validate functionality before production

2. **Document Changes**
   - Maintain configuration changelog
   - Document reason for changes

3. **Backup Before Changes**
   - Export current configuration
   - Create database backup

4. **Monitor After Changes**
   - Check application logs
   - Monitor performance metrics
   - Verify user access

## Next Steps

After configuring application settings:

1. Set up [Target Applications](../targets/)
2. Configure [Agent Installation](../agent/)
3. Complete [Post-Installation](../post-install/) verification

---

© 2025 EPMware, Inc. All rights reserved.
