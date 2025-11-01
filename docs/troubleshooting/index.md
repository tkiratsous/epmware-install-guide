# Troubleshooting

This section provides solutions for common issues encountered during EPMware installation and operation.

## Overview

This troubleshooting guide covers common installation issues, error messages, and their resolutions. Use this guide to diagnose and resolve problems with EPMware components.

## Installation Issues

### Database Installation

#### ORA-01017: Invalid username/password

**Error:**
```
ORA-01017: invalid username/password; logon denied
```

**Cause:** Incorrect credentials or case-sensitive password

**Solution:**
```sql
-- Verify username exists
SELECT username FROM dba_users WHERE username = 'EW';

-- Reset password if needed
ALTER USER ew IDENTIFIED BY new_password;

-- Check password case sensitivity
SHOW PARAMETER sec_case_sensitive_logon;
```

#### ORA-01652: Unable to extend temp segment

**Error:**
```
ORA-01652: unable to extend temp segment by 128 in tablespace TEMP
```

**Cause:** Insufficient temporary tablespace

**Solution:**
```sql
-- Add space to temp tablespace
ALTER TABLESPACE temp ADD DATAFILE '/u01/app/oracle/oradata/temp02.dbf' SIZE 1G;

-- Or resize existing file
ALTER DATABASE DATAFILE '/u01/app/oracle/oradata/temp01.dbf' RESIZE 2G;
```

#### ORA-00054: Resource busy

**Error:**
```
ORA-00054: resource busy and acquire with NOWAIT specified
```

**Cause:** Object locked by another session

**Solution:**
```sql
-- Find locking session
SELECT s.sid, s.serial#, s.username, l.type
FROM v$session s, v$lock l
WHERE s.sid = l.sid
AND l.id1 = (SELECT object_id FROM user_objects WHERE object_name = 'TABLE_NAME');

-- Kill session if necessary
ALTER SYSTEM KILL SESSION 'sid,serial#' IMMEDIATE;
```

### Application Server Issues

#### Tomcat Won't Start

**Symptoms:**
- Service fails to start
- No catalina.out generated
- Port already in use error

**Solutions:**

1. **Check port availability:**
```bash
# Linux
netstat -tuln | grep 8080
lsof -i :8080

# Windows
netstat -ano | findstr :8080
```

2. **Verify Java installation:**
```bash
java -version
echo $JAVA_HOME
```

3. **Check permissions:**
```bash
ls -la /opt/tomcat/
chown -R tomcat:tomcat /opt/tomcat/
```

4. **Review logs:**
```bash
tail -100 /opt/tomcat/logs/catalina.out
```

#### Out of Memory Errors

**Error:**
```
java.lang.OutOfMemoryError: Java heap space
```

**Solution:**

Edit setenv.sh or setenv.bat:
```bash
# Increase heap size
export CATALINA_OPTS="$CATALINA_OPTS -Xms8192m -Xmx12288m"

# Add heap dump on OOM
export CATALINA_OPTS="$CATALINA_OPTS -XX:+HeapDumpOnOutOfMemoryError"
export CATALINA_OPTS="$CATALINA_OPTS -XX:HeapDumpPath=/opt/tomcat/logs"
```

## Deployment Issues

### WAR File Not Deploying

**Symptoms:**
- WAR file present but not extracted
- Application not accessible
- Deployment errors in logs

**Solutions:**

1. **Check autodeploy setting:**
```xml
<!-- In server.xml -->
<Host name="localhost" appBase="webapps"
      unpackWARs="true" autoDeploy="true">
```

2. **Verify WAR file integrity:**
```bash
jar -tf epmware.war | head
```

3. **Check disk space:**
```bash
df -h /opt/tomcat/webapps/
```

4. **Manual deployment:**
```bash
cd /opt/tomcat/webapps
mkdir epmware
cd epmware
jar -xvf ../epmware.war
```

### Database Connection Failures

**Error:**
```
Cannot create JDBC connection
Connection refused
```

**Solutions:**

1. **Verify database is running:**
```bash
# Check listener
lsnrctl status

# Check database
sqlplus / as sysdba
SQL> SELECT status FROM v$instance;
```

2. **Test connection string:**
```bash
# Test with sqlplus
sqlplus ew/password@//hostname:1521/servicename
```

3. **Check JDBC configuration:**
```properties
# In fs_custom.properties
jdbc.url=jdbc:oracle:thin:@//hostname:1521/servicename
jdbc.user=ew
jdbc.password=correct_password
```

4. **Verify firewall rules:**
```bash
telnet hostname 1521
```

## Agent Issues

### Agent Not Starting

**Symptoms:**
- Service won't start
- No PID file created
- Agent not visible in UI

**Solutions:**

1. **Check Java:**
```bash
which java
java -version
```

2. **Verify configuration:**
```bash
cat /opt/epmware/agent/conf/agent.properties
```

3. **Check permissions:**
```bash
ls -la /opt/epmware/agent/
ps aux | grep agent
```

4. **Review logs:**
```bash
tail -f /opt/epmware/agent/logs/agent.log
```

### Agent Registration Failed

**Error:**
```
Agent registration failed: Invalid key
```

**Solutions:**

1. **Generate new key:**
   - Login to EPMware
   - Navigate to Configuration → Agents
   - Generate new registration key

2. **Re-register agent:**
```bash
/opt/epmware/agent/bin/agent.sh register --key=NEW_KEY
```

3. **Check connectivity:**
```bash
curl https://epmware.company.com/api/agent/register
```

## Target Application Issues

### Planning Connection Failed

**Error:**
```
Failed to authorize user for LCM migrations
```

**Solution:**

Grant LCM Administrator role:
1. Access Shared Services Console
2. Navigate to User Management
3. Assign LCM Administrator role to EPMware user
4. Restart Planning services

### HFM reg.properties Not Found

**Error:**
```
Cannot locate reg.properties file
```

**Solution:**

Copy reg.properties:
```batch
copy D:\Oracle\Middleware\user_projects\config\foundation\11.1.2.0\reg.properties ^
     D:\Oracle\Middleware\user_projects\epmsystem1\config\foundation\11.1.2.0\
```

### Cloud Connection Timeout

**Error:**
```
EPMAutomate connection timeout
```

**Solutions:**

1. **Check internet connectivity:**
```bash
ping cloud.oracle.com
```

2. **Verify proxy settings:**
```bash
export HTTP_PROXY=http://proxy:8080
export HTTPS_PROXY=http://proxy:8080
```

3. **Test EPMAutomate:**
```bash
epmautomate login user password url
```

## Performance Issues

### Slow Response Times

**Symptoms:**
- Page load times > 5 seconds
- Timeout errors
- High CPU/memory usage

**Solutions:**

1. **Check system resources:**
```bash
top
free -h
iostat -x 1
```

2. **Analyze database performance:**
```sql
-- Check slow queries
SELECT * FROM v$sql 
WHERE elapsed_time > 1000000
ORDER BY elapsed_time DESC;

-- Check wait events
SELECT event, total_waits, time_waited
FROM v$system_event
WHERE event NOT LIKE 'SQL*Net%'
ORDER BY time_waited DESC;
```

3. **Review application logs:**
```bash
grep "ERROR\|WARN" /opt/tomcat/logs/epmware.log
```

4. **Optimize connection pools:**
```properties
jdbc.maxPoolSize=100
jdbc.minPoolSize=20
```

## Security Issues

### SSL Certificate Errors

**Error:**
```
javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException
```

**Solutions:**

1. **Import certificate:**
```bash
keytool -import -alias epmware -file certificate.crt \
        -keystore $JAVA_HOME/jre/lib/security/cacerts \
        -storepass changeit
```

2. **Verify certificate:**
```bash
keytool -list -v -keystore keystore.jks
```

3. **Disable verification (dev only):**
```properties
ssl.verify.hostname=false
ssl.trust.all.certificates=true
```

### Authentication Failures

**Error:**
```
LDAP authentication failed
```

**Solutions:**

1. **Test LDAP connectivity:**
```bash
ldapsearch -x -H ldap://ldap.company.com \
           -D "cn=admin,dc=company,dc=com" \
           -W -b "dc=company,dc=com"
```

2. **Verify LDAP configuration:**
```properties
ldap.url=ldap://ldap.company.com:389
ldap.base.dn=dc=company,dc=com
ldap.user.search.filter=(sAMAccountName={0})
```

## Common Error Messages

### Error Reference Table

| Error Code | Message | Solution |
|------------|---------|----------|
| EW-001 | Database connection failed | Check JDBC settings |
| EW-002 | Invalid credentials | Verify username/password |
| EW-003 | Agent not responding | Check agent status |
| EW-004 | Deployment failed | Review deployment logs |
| EW-005 | Import failed | Check source data format |
| EW-006 | Workflow error | Verify workflow configuration |
| EW-007 | Email send failed | Check SMTP settings |
| EW-008 | License expired | Renew license |
| EW-009 | Out of memory | Increase heap size |
| EW-010 | Connection timeout | Check network/firewall |

## Logging and Diagnostics

### Enable Debug Logging

1. **Application debug:**
```properties
# In log4j.properties
log4j.logger.com.epmware=DEBUG
log4j.logger.org.springframework=DEBUG
```

2. **Database debug:**
```properties
# In fs_custom.properties
debug.sql=true
debug.sql.timing=true
```

3. **Agent debug:**
```properties
# In agent.properties
debug.enabled=true
log.level=DEBUG
```

### Log File Locations

| Component | Log Location |
|-----------|--------------|
| Database | $ORACLE_BASE/diag/rdbms/... |
| Tomcat | /opt/tomcat/logs/catalina.out |
| EPMware | /opt/tomcat/logs/epmware.log |
| Agent | /opt/epmware/agent/logs/agent.log |
| Deployment | /opt/epmware/logs/deployment.log |

## Recovery Procedures

### Database Recovery

```sql
-- Restore from backup
impdp ew/password directory=DATA_PUMP dumpfile=backup.dmp

-- Rebuild invalid objects
EXEC DBMS_UTILITY.compile_schema('EW');

-- Verify recovery
SELECT COUNT(*) FROM user_objects WHERE status = 'INVALID';
```

### Application Recovery

```bash
# Restore application
systemctl stop tomcat
rm -rf /opt/tomcat/webapps/epmware
cp backup/epmware.war /opt/tomcat/webapps/
systemctl start tomcat
```

## Getting Help

### Gather Diagnostic Information

Before contacting support, gather:

1. **System information:**
```bash
uname -a
java -version
cat /etc/redhat-release
```

2. **Error logs:**
```bash
tar -czvf logs.tar.gz /opt/tomcat/logs/ /opt/epmware/logs/
```

3. **Configuration files:**
```bash
tar -czvf config.tar.gz /opt/tomcat/conf/ /opt/epmware/config/
```

### Contact Support

**EPMware Support:**
- Email: support@epmware.com
- Phone: 408-614-0442
- Portal: support.epmware.com

**Information to provide:**
- EPMware version
- Error messages
- Steps to reproduce
- Log files
- Configuration files

---

© 2025 EPMware, Inc. All rights reserved.
