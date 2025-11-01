# Cloud Applications (PCMCS/EPBCS)

This section covers the configuration of Oracle Cloud EPM applications including PCMCS, EPBCS, FCCS, and other cloud services.

## Overview

Cloud application integration uses EPMAutomate utility for metadata deployment and requires specific setup for connectivity and authentication.

## Prerequisites

Before configuring cloud applications:

- [ ] Cloud service provisioned and accessible
- [ ] Service Administrator role assigned
- [ ] EPMAutomate utility installed
- [ ] EPMware agent deployed (cloud or on-premise)
- [ ] Internet connectivity established
- [ ] SSL certificates valid

## EPMAutomate Installation

EPMAutomate is required for cloud application integration.

### Download EPMAutomate

1. **Access Oracle Support**
   - Login to Oracle Support Portal
   - Search for EPMAutomate
   - Download latest version

2. **Alternative Download**
   - From cloud instance: Settings → Downloads
   - Select EPMAutomate for your OS

### Install EPMAutomate

#### Linux Installation

```bash
# Extract EPMAutomate
unzip epmautomate.zip -d /opt/epmware/

# Create symbolic link
ln -s /opt/epmware/epmautomate/bin/epmautomate.sh /usr/local/bin/epmautomate

# Verify installation
epmautomate --version
```

#### Windows Installation

```batch
:: Extract to installation directory
:: Extract epmautomate.zip to D:\epmware\epmautomate

:: Add to PATH
setx PATH "%PATH%;D:\epmware\epmautomate\bin"

:: Verify installation
epmautomate.bat --version
```

### EPMAutomate Directory Structure

For EPMware agent integration:

```
/home/ew_agent/
└── epmautomate/
    ├── bin/
    │   ├── epmautomate.sh
    │   └── epmautomate.bat
    ├── lib/
    └── jre/
```

## Cloud Service Configuration

### PCMCS Configuration

Configure PCMCS application in EPMware:

```properties
# PCMCS Configuration
pcmcs.url=https://pcmcs-test-a123456.epm.em3.oraclecloud.com
pcmcs.identity.domain=mycompany
pcmcs.username=svc_epmware
pcmcs.password=encrypted_password

# EPMAutomate Settings
pcmcs.epmautomate.path=/opt/epmware/epmautomate/bin
pcmcs.epmautomate.timeout=3600
pcmcs.epmautomate.retry.count=3
```

### EPBCS Configuration

Configure EPBCS application:

```properties
# EPBCS Configuration
epbcs.url=https://planning-test-mycompany.pbcs.us2.oraclecloud.com
epbcs.identity.domain=mycompany
epbcs.username=svc_epmware
epbcs.password.file=/opt/epmware/config/epbcs_pwd.epw

# Connection Settings
epbcs.connection.proxy=proxy.company.com:8080
epbcs.connection.timeout=300
epbcs.connection.ssl.verify=true
```

### FCCS Configuration

Configure FCCS application:

```properties
# FCCS Configuration
fccs.url=https://consolidation-mycompany.fccs.us6.oraclecloud.com
fccs.identity.domain=mycompany
fccs.service.type=FCCS
fccs.api.version=v1
```

## Authentication Methods

### Password File Authentication

Create encrypted password file:

```bash
# Create password file
epmautomate encrypt P@ssw0rd myKey passwordFile.epw

# Use in login
epmautomate login username passwordFile.epw https://instance.oraclecloud.com
```

### OAuth Authentication

Configure OAuth for API access:

```properties
# OAuth Configuration
oauth.enabled=true
oauth.client.id=client_id_here
oauth.client.secret=client_secret_here
oauth.token.url=https://identity.oraclecloud.com/oauth/tokens
oauth.scope=urn:opc:resource:consumer::all
```

## Test Connectivity

### Manual Connection Test

Test EPMAutomate connectivity:

```bash
# Navigate to EPMAutomate directory
cd /opt/epmware/epmautomate/bin

# Test login
./epmautomate login svc_epmware P@ssw0rd https://pcmcs-test.oraclecloud.com

# Expected output
Login successful

# Logout
./epmautomate logout
```

### Automated Connection Test

Configure automated testing:

```properties
# Connection Test Settings
test.connection.enabled=true
test.connection.schedule=0 */30 * * * ?  # Every 30 minutes
test.connection.timeout=60
test.connection.alert.on.failure=true
```

## EPMAutomate Commands

### Common EPMAutomate Operations

| Command | Purpose | Example |
|---------|---------|---------|
| login | Connect to service | `epmautomate login user pwd url` |
| uploadfile | Upload file | `epmautomate uploadfile file.zip` |
| importmetadata | Import metadata | `epmautomate importmetadata JOB_NAME` |
| rundatarule | Run business rule | `epmautomate rundatarule RULE_NAME` |
| exportmetadata | Export metadata | `epmautomate exportmetadata JOB_NAME` |
| downloadfile | Download file | `epmautomate downloadfile file.zip` |
| logout | Disconnect | `epmautomate logout` |

### Metadata Operations

#### Upload Metadata File

```bash
# Upload metadata file
epmautomate uploadfile /tmp/metadata.zip inbox

# Import metadata
epmautomate importmetadata ImportJob ZIP_FILE=metadata.zip
```

#### Export Metadata

```bash
# Export metadata
epmautomate exportmetadata ExportJob

# Download export file
epmautomate downloadfile outbox/export.zip
```

## Deployment Configuration

### Configure Deployment Jobs

```properties
# Deployment Job Configuration
deployment.job.name=EPMware_Deploy
deployment.job.type=METADATA
deployment.job.import.mode=REPLACE
deployment.job.validate=true
deployment.job.error.handling=ABORT
```

### Batch Deployment

Configure batch deployment for cloud:

```properties
# Batch Settings
batch.enabled=true
batch.schedule=0 0 22 * * ?  # 10 PM daily
batch.max.file.size=100MB
batch.compression=true
batch.encryption=true
```

## Data Management

### Data Import Configuration

```properties
# Data Import Settings
data.import.enabled=true
data.import.format=CSV
data.import.delimiter=,
data.import.date.format=MM/dd/yyyy
data.import.decimal.separator=.
```

### Data Export Configuration

```properties
# Data Export Settings
data.export.enabled=true
data.export.format=COLUMNAR
data.export.compression=true
data.export.encryption=true
```

## Migration and Backup

### Snapshot Management

```properties
# Snapshot Configuration
snapshot.enabled=true
snapshot.schedule=0 0 1 * * ?  # 1 AM daily
snapshot.retention.count=7
snapshot.name.pattern=EPMware_${date}
```

### LCM Migration

```bash
# Export LCM artifact
epmautomate exportSnapshot SNAPSHOT_NAME

# Download snapshot
epmautomate downloadfile SNAPSHOT_NAME.zip

# Upload to target
epmautomate uploadfile SNAPSHOT_NAME.zip

# Import snapshot
epmautomate importSnapshot SNAPSHOT_NAME
```

## EPMAutomate Maintenance

### Update EPMAutomate

Keep EPMAutomate updated:

```bash
# Check for updates
epmautomate upgrade

# If update available
EPMAutomate version 24.01.55 is available for download

# Perform upgrade
epmautomate upgrade
```

### Version Management

```properties
# Version Check Settings
version.check.enabled=true
version.check.schedule=0 0 6 * * MON  # Weekly Monday 6 AM
version.auto.upgrade=false
version.notification.enabled=true
```

## Security Configuration

### Proxy Configuration

For environments with proxy:

```properties
# Proxy Settings
proxy.enabled=true
proxy.host=proxy.company.com
proxy.port=8080
proxy.username=proxy_user
proxy.password=encrypted_password
proxy.bypass=localhost,127.0.0.1
```

### SSL Configuration

```properties
# SSL Settings
ssl.verify.hostname=true
ssl.trust.all.certificates=false
ssl.keystore.path=/opt/epmware/certs/keystore.jks
ssl.keystore.password=changeit
ssl.truststore.path=/opt/epmware/certs/truststore.jks
ssl.truststore.password=changeit
```

## Performance Optimization

### Connection Pooling

```properties
# Connection Pool
pool.enabled=true
pool.min.connections=2
pool.max.connections=10
pool.timeout=300
pool.idle.timeout=600
```

### Upload/Download Optimization

```properties
# Transfer Optimization
transfer.chunk.size=10MB
transfer.parallel.threads=4
transfer.compression.level=6
transfer.retry.on.failure=true
```

## Monitoring and Alerts

### Service Monitoring

```properties
# Monitoring Configuration
monitor.service.health=true
monitor.api.availability=true
monitor.response.time=true
monitor.error.rate=true

# Alert Thresholds
alert.response.time.threshold=5000
alert.error.rate.threshold=5
alert.availability.threshold=99.5
```

### Job Monitoring

```properties
# Job Monitoring
job.monitor.enabled=true
job.monitor.timeout=3600
job.monitor.status.check.interval=60
job.alert.on.failure=true
job.alert.on.warning=true
```

## Troubleshooting

### Common Issues

**Login Failed**
```
Error: Invalid credentials
Solution: Verify username, password, and URL
Check: Identity domain correct
```

**EPMAutomate Not Found**
```
Error: epmautomate command not found
Solution: Add EPMAutomate to PATH
Verify: Installation directory correct
```

**Connection Timeout**
```
Error: Connection to cloud service timed out
Solution: Check internet connectivity
Verify: Proxy settings if applicable
```

**SSL Certificate Error**
```
Error: SSL certificate verification failed
Solution: Update certificates
Or: Set ssl.verify=false (not recommended for production)
```

### Debug Mode

Enable debugging:

```bash
# Enable EPMAutomate debug
export EPM_AUTOMATE_DEBUG=true
epmautomate login user pwd url

# Enable verbose output
epmautomate login user pwd url -v
```

### Log Files

| Log File | Location | Content |
|----------|----------|---------|
| epmautomate.log | ~/.oracle/epmautomate | EPMAutomate operations |
| agent.log | /opt/epmware/agent/logs | Agent operations |
| deployment.log | /opt/epmware/logs | Deployment details |

## Best Practices

1. **Credential Management**
   - Use password files, not plain text
   - Rotate passwords regularly
   - Use service accounts

2. **EPMAutomate Maintenance**
   - Keep utility updated
   - Test updates in non-production
   - Monitor Oracle notifications

3. **Performance**
   - Use compression for large files
   - Schedule during maintenance windows
   - Monitor service limits

4. **Error Handling**
   - Implement retry logic
   - Log all operations
   - Alert on failures

## Cloud-Specific Considerations

### Service Limits

Be aware of cloud service limits:

| Limit | Value | Notes |
|-------|-------|-------|
| File Size | 2GB | Maximum upload size |
| API Calls | 500/min | Rate limiting |
| Concurrent Jobs | 5 | Per service instance |
| Retention | 60 days | Snapshot retention |

### Maintenance Windows

Oracle Cloud maintenance schedule:

- Daily: 1-hour window (configured)
- Monthly: Patch updates (first Friday)
- Quarterly: Major updates

## Next Steps

After configuring cloud applications:

1. Test EPMAutomate connectivity
2. Create deployment jobs
3. Schedule automated tasks
4. Configure monitoring

---

© 2025 EPMware, Inc. All rights reserved.
