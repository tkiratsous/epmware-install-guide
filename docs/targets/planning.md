# Planning Classic

This section covers the configuration of Oracle Hyperion Planning Classic applications in EPMware.

## Overview

Oracle Hyperion Planning Classic integration requires specific configuration including password file generation, LCM permissions, and application properties setup.

## Prerequisites

Before configuring Planning Classic:

- [ ] Planning application installed and running
- [ ] Administrator access to Planning
- [ ] LCM Administrator role assigned to EPMware user
- [ ] Network connectivity between EPMware and Planning
- [ ] EPMware agent installed on Planning server

## Password File Generation

EPMware requires an encrypted password file to authenticate with Planning applications.

### Generate Password File

!!! note "Important"
    The password file must be generated on the Planning application server using the Planning utilities.

#### Steps to Generate Password File

1. **Log on to Planning Server**
   ```bash
   ssh planning_server
   ```

2. **Navigate to Planning Directory**
   ```bash
   # Linux
   cd /Oracle/Middleware/user_projects/epmsystem1/Planning/planning1
   
   # Windows
   cd D:\Oracle\Middleware\user_projects\epmsystem1\Planning\planning1
   ```

3. **Run Password Encryption Utility**
   
   For Windows:
   ```batch
   PasswordEncryption.cmd ew_hp_cl_pwd.txt
   ```
   
   For Linux:
   ```bash
   ./PasswordEncryption.sh ew_hp_cl_pwd.txt
   ```

4. **Enter Password When Prompted**
   ```
   Enter password to encrypt: ********
   Password has been encrypted and written to the file ew_hp_cl_pwd.txt successfully!
   ```

### Password File Location

Store the password file in a secure location:

- Linux: `/opt/epmware/config/planning_pwd.txt`
- Windows: `D:\epmware\config\planning_pwd.txt`

### Configure Password File in EPMware

1. Navigate to **Configuration → Applications**
2. Select your Planning application
3. Locate **PASSWORD_FILE** property
4. Right-click and select **Edit Properties**
5. Enter the full path to the password file
6. Save configuration

## LCM Administrator Role

The EPMware service account requires LCM Administrator privileges.

### Grant LCM Administrator Role

1. **Access Planning Workspace**
   ```
   http://planning_server:19000/workspace
   ```

2. **Navigate to Shared Services**
   - Administration → Shared Services Console

3. **Assign LCM Role**
   - Navigate to User Management
   - Find EPMware service account
   - Assign roles:
     - LCM Administrator
     - Planning Application Administrator (optional)
     - Provisioning Manager (optional)

### Verify LCM Access

Test LCM access using EPMware service account:

```bash
# Test LCM export
./Utility.sh /A:appname /U:epmware_user /P:password /EXPORT
```

!!! warning "LCM Error"
    If you see "Failed to authorize user for LCM migrations", the LCM Administrator role is not properly assigned.

## Application Configuration

### Basic Configuration

Configure Planning application properties in EPMware:

| Property | Value | Description |
|----------|-------|-------------|
| APP_NAME | PLANNING1 | Application name in Planning |
| APP_URL | http://server:8300/HyperionPlanning | Planning URL |
| INSTANCE_NAME | planning1 | Planning instance name |
| EPM_INSTANCE_HOME | /Oracle/Middleware/user_projects/epmsystem1 | EPM instance path |
| PASSWORD_FILE | /opt/epmware/config/pwd.txt | Password file path |
| PLANNING_USER | admin | Planning administrator user |

### Advanced Configuration

```properties
# Planning Configuration
planning.app.name=PLANNING1
planning.server.url=http://planning.company.com:8300
planning.admin.user=admin
planning.password.file=/opt/epmware/config/planning_pwd.txt

# Import Settings
planning.import.enabled=true
planning.import.type=LCM
planning.import.dimensions=Entity,Account,Product,Version,Scenario
planning.import.preserve.formulas=true
planning.import.preserve.member.order=true

# Deployment Settings  
planning.deployment.enabled=true
planning.deployment.method=OUTLINE_LOAD
planning.deployment.validate=true
planning.deployment.clear.all.data=false
planning.deployment.restructure.database=true

# Refresh Settings
planning.refresh.database=true
planning.refresh.security.filters=true
planning.refresh.create.blocks=false
```

## Outline Load Utility Configuration

For direct outline loads:

### Configure Outline Load

```properties
# Outline Load Settings
outline.load.utility.path=/Oracle/Middleware/EPMSystem11R1/products/Planning/bin
outline.load.command=OutlineLoad.sh
outline.load.parameters=/A:${app} /U:${user} /M /N /I:${file} /D:${dimension} /L:${log}
outline.load.delimiter=,
outline.load.escape.character=\
```

### Outline Load Options

| Parameter | Description | Example |
|-----------|-------------|---------|
| /A | Application name | /A:PLANNING1 |
| /U | Username | /U:admin |
| /M | Use password file | /M |
| /N | No validation | /N |
| /I | Input file | /I:/tmp/metadata.csv |
| /D | Dimension name | /D:Entity |
| /L | Log file | /L:/tmp/outline.log |

## Metadata Import Process

### Configure Auto-Import

Enable automatic metadata import from Planning:

```properties
# Auto Import Configuration
import.auto.enabled=true
import.auto.schedule=0 0 2 * * ?  # Daily at 2 AM
import.auto.dimensions=ALL
import.auto.backup.before=true
import.auto.notification.enabled=true
```

### Manual Import Steps

1. Navigate to **Metadata → Import**
2. Select Planning application
3. Choose dimensions:
   - Account
   - Entity  
   - Product
   - Version
   - Scenario
   - Custom dimensions
4. Set import options:
   - Clear existing metadata
   - Preserve local changes
   - Validate after import
5. Click **Import**
6. Review import log

## Deployment Configuration

### Direct Deployment

Configure direct deployment to Planning:

```properties
# Direct Deployment
deployment.direct.enabled=true
deployment.direct.method=OUTLINE_LOAD
deployment.direct.async=false
deployment.direct.timeout=3600
```

### Batch Deployment

Configure batch deployment:

```properties
# Batch Deployment
deployment.batch.enabled=true
deployment.batch.schedule=0 0 22 * * ?  # 10 PM daily
deployment.batch.size=5000
deployment.batch.parallel.dimensions=false
```

## Security Filter Management

### Configure Security Filters

```properties
# Security Filter Settings
security.filter.enabled=true
security.filter.refresh.after.deployment=true
security.filter.validate.before.deployment=true
security.filter.backup.existing=true
```

### Security Filter Deployment

1. Define filters in EPMware
2. Map to Planning users/groups
3. Deploy filters
4. Refresh security in Planning

## Calculation Scripts

### Configure Calculation Scripts

```properties
# Calculation Settings
calc.run.after.deployment=true
calc.script.default=AggAll
calc.script.timeout=1800
calc.script.log.enabled=true
```

### Common Calculations

| Script | Purpose | When to Run |
|--------|---------|-------------|
| AggAll | Aggregate all data | After metadata changes |
| CalcAll | Calculate all data | After data load |
| ClearAll | Clear all data | Before major restructure |

## Business Rules

### Business Rule Integration

```properties
# Business Rule Settings
business.rule.enabled=true
business.rule.runtime.prompts=true
business.rule.sequence=RuleSet1,RuleSet2
business.rule.error.handling=CONTINUE
```

## Validation and Testing

### Test Connection

Test Planning connection:

1. Navigate to **Configuration → Applications**
2. Select Planning application
3. Click **Test Connection**
4. Verify:
   - Network connectivity
   - Authentication
   - API availability
   - LCM access

### Validation Checklist

- [ ] Password file created and accessible
- [ ] LCM Administrator role assigned
- [ ] Application properties configured
- [ ] Import test successful
- [ ] Deployment test successful
- [ ] Security filters working
- [ ] Calculations running

## Troubleshooting

### Common Issues

**Password File Not Found**
```
Error: Cannot locate password file
Solution: Verify file path and permissions
```

**LCM Authorization Failed**
```
Error: Failed to authorize user for LCM migrations
Solution: Grant LCM Administrator role in Shared Services
```

**Outline Load Failed**
```
Error: Outline load utility error
Solution: Check dimension name and data format
```

**Connection Timeout**
```
Error: Connection to Planning timed out
Solution: Verify Planning services are running
```

### Debug Mode

Enable debug logging:

```properties
# Debug Settings
debug.planning.enabled=true
debug.planning.api.calls=true
debug.planning.lcm.operations=true
debug.planning.outline.load=true
```

### Log Files

Check these logs for issues:

| Log File | Location | Content |
|----------|----------|---------|
| Planning.log | $EPM_ORACLE_HOME/diagnostics/logs | Planning application log |
| OutlineLoad.log | /tmp/ | Outline load results |
| LCM.log | $EPM_ORACLE_HOME/diagnostics/logs | LCM operations |
| EPMware.log | /opt/epmware/logs | EPMware operations |

## Best Practices

1. **Password Management**
   - Rotate passwords regularly
   - Use strong passwords
   - Secure password file access

2. **LCM Operations**
   - Schedule during off-hours
   - Backup before import
   - Validate after deployment

3. **Performance**
   - Optimize outline load files
   - Use incremental updates
   - Monitor calculation times

4. **Maintenance**
   - Regular database restructure
   - Archive old data
   - Monitor application size

## Next Steps

After configuring Planning Classic:

1. Configure other target applications
2. Set up deployment schedules
3. Create workflows
4. Test end-to-end process

---

© 2025 EPMware, Inc. All rights reserved.
