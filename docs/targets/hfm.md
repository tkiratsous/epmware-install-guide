# Hyperion HFM

This section covers the configuration of Oracle Hyperion Financial Management (HFM) Classic applications in EPMware.

## Overview

HFM integration requires specific configuration steps including reg.properties file setup and Windows-specific components for on-premise installations.

## Prerequisites

Before configuring HFM:

- [ ] HFM application installed and running
- [ ] HFM Administrator access
- [ ] EPMware agent installed on HFM server
- [ ] Cygwin installed (Windows servers)
- [ ] reg.properties file configured

## reg.properties Configuration

The reg.properties file is required for HFM integration on Windows servers.

### Copy reg.properties File

!!! important "Required for HFM"
    This step is critical for HFM connectivity. The reg.properties file must be copied to the correct EPM instance directory.

#### Steps to Configure reg.properties

1. **Log on to HFM Application Server**
   ```
   Remote Desktop to HFM server
   ```

2. **Locate Source reg.properties**
   
   Source location:
   ```
   D:\Oracle\Middleware\user_projects\config\foundation\11.1.2.0\reg.properties
   ```

3. **Copy to EPM Instance Directory**
   
   Destination location:
   ```
   D:\Oracle\Middleware\user_projects\epmsystem1\config\foundation\11.1.2.0\
   ```

   Command:
   ```batch
   copy D:\Oracle\Middleware\user_projects\config\foundation\11.1.2.0\reg.properties ^
        D:\Oracle\Middleware\user_projects\epmsystem1\config\foundation\11.1.2.0\
   ```

4. **Verify File Copied**
   ```batch
   dir D:\Oracle\Middleware\user_projects\epmsystem1\config\foundation\11.1.2.0\reg.properties
   ```

### reg.properties Content

Verify the reg.properties file contains HFM configuration:

```properties
# HFM Configuration
HFM.ApplicationServer=HFMSERVER
HFM.ClusterName=HFMCLUSTER
HFM.DatabaseServer=DBSERVER
HFM.DatabaseName=HFMDB
HFM.WebServer=http://hfm.company.com
HFM.WebServerPort=80
```

## Application Configuration

### Basic HFM Configuration

Configure HFM application properties in EPMware:

| Property | Value | Description |
|----------|-------|-------------|
| APP_NAME | HFM_PROD | HFM application name |
| APP_SERVER | HFMSERVER | HFM application server |
| CLUSTER_NAME | HFMCLUSTER | HFM cluster name |
| DATABASE_SERVER | DBSERVER | HFM database server |
| DATABASE_NAME | HFMDB | HFM database name |
| HFM_USER | admin | HFM administrator |

### Advanced Configuration

```properties
# HFM Configuration
hfm.app.name=HFM_PROD
hfm.server.name=HFMSERVER
hfm.cluster.name=HFMCLUSTER
hfm.database.server=DBSERVER
hfm.database.name=HFMDB

# Connection Settings
hfm.connection.timeout=300
hfm.connection.retry.count=3
hfm.connection.pool.size=10

# Import Settings
hfm.import.enabled=true
hfm.import.method=NATIVE
hfm.import.dimensions=Entity,Account,Custom1,Custom2
hfm.import.preserve.data=true

# Deployment Settings
hfm.deployment.enabled=true
hfm.deployment.method=METADATA_LOAD
hfm.deployment.validate=true
hfm.deployment.backup.before=true
```

## Windows Specific Configuration

### Cygwin Installation

Cygwin is required on Windows servers for script execution.

#### Install Cygwin

1. **Download Cygwin**
   - Visit [www.cygwin.com](http://www.cygwin.com)
   - Download setup-x86_64.exe

2. **Run Installation**
   ```
   setup-x86_64.exe
   ```

3. **Select Installation Options**
   - Install from Internet
   - Root Directory: `C:\cygwin`
   - Install for: All Users
   - Local Package Directory: `C:\cygwin\packages`

4. **Select Packages**
   - Base packages (default)
   - SSH utilities
   - Perl
   - Python

5. **Complete Installation**

### Configure Cygwin Environment

```bash
# Add to Windows PATH
C:\cygwin\bin

# Verify installation
cygwin --version
```

## HFM API Configuration

### Configure HFM Web Services

```properties
# HFM Web Services
hfm.ws.url=http://hfm.company.com/hfm/webservices
hfm.ws.timeout=60000
hfm.ws.auth.type=BASIC
hfm.ws.username=admin
hfm.ws.password=encrypted_password
```

### HFM SDK Configuration

```properties
# HFM SDK Settings
hfm.sdk.path=D:\Oracle\Middleware\EPMSystem11R1\products\FinancialManagement\SDK
hfm.sdk.lib.path=D:\Oracle\Middleware\EPMSystem11R1\products\FinancialManagement\lib
hfm.sdk.timeout=300
```

## Metadata Management

### Dimension Configuration

Configure HFM dimensions in EPMware:

| Dimension | HFM Name | Type | Required |
|-----------|----------|------|----------|
| Entity | Entity | Hierarchy | Yes |
| Account | Account | Hierarchy | Yes |
| Scenario | Scenario | List | Yes |
| Period | Period | System | Yes |
| Year | Year | System | Yes |
| Value | Value | System | Yes |
| ICP | ICP | Hierarchy | No |
| Custom1-4 | Custom1-4 | Hierarchy | No |

### Metadata Import

#### Configure Auto-Import

```properties
# Auto Import from HFM
import.hfm.enabled=true
import.hfm.schedule=0 0 3 * * ?  # 3 AM daily
import.hfm.dimensions=Entity,Account,Custom1,Custom2
import.hfm.include.descriptions=true
import.hfm.include.aliases=true
```

#### Manual Import Process

1. Navigate to **Metadata → Import**
2. Select HFM application
3. Choose dimensions
4. Set import options:
   - Include consolidation methods
   - Include security classes
   - Include ICPs
5. Click **Import**
6. Review results

## Rules and Calculations

### HFM Rules Management

```properties
# Rules Configuration
hfm.rules.enabled=true
hfm.rules.path=D:\HFM\Rules
hfm.rules.backup.before.load=true
hfm.rules.validate.before.load=true
hfm.rules.compile.after.load=true
```

### Calculation Scripts

```properties
# Calculation Settings
hfm.calc.enabled=true
hfm.calc.default.scenario=Actual
hfm.calc.default.year=2024
hfm.calc.default.period=January
hfm.calc.timeout=1800
```

## Security Configuration

### HFM Security Classes

```properties
# Security Class Management
hfm.security.class.enabled=true
hfm.security.class.import=true
hfm.security.class.deploy=true
hfm.security.class.sync.with.ad=true
```

### User Provisioning

```properties
# User Provisioning
hfm.user.provisioning.enabled=true
hfm.user.provisioning.method=NATIVE
hfm.user.default.security.class=DefaultClass
hfm.user.sync.with.shared.services=true
```

## Data Management

### Data Extract Configuration

```properties
# Data Extract Settings
hfm.data.extract.enabled=true
hfm.data.extract.format=COMMA_DELIMITED
hfm.data.extract.path=D:\HFM\Extracts
hfm.data.extract.compress=true
```

### Data Load Configuration

```properties
# Data Load Settings
hfm.data.load.enabled=false
hfm.data.load.format=COMMA_DELIMITED
hfm.data.load.path=D:\HFM\DataFiles
hfm.data.load.validate=true
hfm.data.load.replace=false
```

## Journal Management

### Journal Configuration

```properties
# Journal Settings
hfm.journal.enabled=true
hfm.journal.auto.approve=false
hfm.journal.require.attachment=true
hfm.journal.audit.enabled=true
```

## Consolidation Settings

### Configure Consolidation

```properties
# Consolidation Configuration
hfm.consolidation.enabled=true
hfm.consolidation.method=PROPORTIONAL
hfm.consolidation.auto.after.load=false
hfm.consolidation.force.translate=true
hfm.consolidation.force.calculate=true
```

## Validation and Testing

### Test HFM Connection

1. Navigate to **Configuration → Applications**
2. Select HFM application
3. Click **Test Connection**
4. Verify:
   - Server connectivity
   - Authentication
   - Application access
   - API availability

### Validation Checklist

- [ ] reg.properties file in place
- [ ] Cygwin installed (Windows)
- [ ] HFM services running
- [ ] Authentication working
- [ ] Metadata import successful
- [ ] Rules loading correctly

## Troubleshooting

### Common Issues

**reg.properties Not Found**
```
Error: Cannot locate reg.properties file
Solution: Copy file to epmsystem1 directory
```

**HFM Connection Failed**
```
Error: Unable to connect to HFM application
Solution: Verify HFM services are running
```

**Authentication Error**
```
Error: Invalid credentials for HFM
Solution: Verify username and password
```

**Metadata Load Failed**
```
Error: Failed to load metadata to HFM
Solution: Check dimension mapping and data format
```

### Debug Logging

Enable debug logging for HFM:

```properties
# Debug Settings
debug.hfm.enabled=true
debug.hfm.connection=true
debug.hfm.metadata.operations=true
debug.hfm.api.calls=true
debug.hfm.rules=true
```

### Log File Locations

| Log File | Location | Purpose |
|----------|----------|---------|
| HFM.log | Oracle\diagnostics\logs | HFM application log |
| HFMWeb.log | Oracle\diagnostics\logs | Web tier log |
| EPMware.log | \opt\epmware\logs | EPMware operations |
| Agent.log | \opt\epmware\agent\logs | Agent operations |

## Performance Optimization

### Connection Pool Tuning

```properties
# Connection Pool Optimization
hfm.pool.min.size=5
hfm.pool.max.size=20
hfm.pool.increment=5
hfm.pool.timeout=300
hfm.pool.validation.query=SELECT 1
```

### Metadata Load Optimization

```properties
# Load Optimization
hfm.load.batch.size=1000
hfm.load.parallel.threads=4
hfm.load.use.bulk.api=true
hfm.load.disable.validation=false
```

## Best Practices

1. **reg.properties Management**
   - Backup before changes
   - Verify after EPM patches
   - Keep synchronized across servers

2. **Metadata Management**
   - Regular metadata validation
   - Incremental updates preferred
   - Backup before major changes

3. **Performance**
   - Monitor connection pool usage
   - Optimize batch sizes
   - Schedule loads during off-hours

4. **Security**
   - Regular security class reviews
   - Audit user access
   - Implement segregation of duties

## Next Steps

After configuring HFM:

1. Test metadata import
2. Configure deployment schedules
3. Set up workflows
4. Implement monitoring

---

© 2025 EPMware, Inc. All rights reserved.
