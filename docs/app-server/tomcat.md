# Tomcat Configuration

This section covers the installation and configuration of Apache Tomcat for the EPMware application.

## Tomcat Installation

### Download Tomcat

Download Apache Tomcat from the official website:

- **URL**: [tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi)
- **Recommended Version**: 8.5.x (e.g., 8.5.66 or later)
- **Supported Versions**: 8.0.44 minimum, 8.5.x recommended, 9.0.x supported

### Installation Process

#### Linux Installation

```bash
# Download Tomcat
cd /tmp
wget https://downloads.apache.org/tomcat/tomcat-8/v8.5.85/bin/apache-tomcat-8.5.85.tar.gz

# Extract to installation directory
sudo tar -xzvf apache-tomcat-8.5.85.tar.gz -C /opt/
sudo mv /opt/apache-tomcat-8.5.85 /opt/tomcat

# Set ownership
sudo chown -R epmware:epmware /opt/tomcat

# Set execute permissions
chmod +x /opt/tomcat/bin/*.sh
```

#### Windows Installation

1. Download the Windows zip file
2. Extract to target location:
```batch
:: Extract to D:\apache-tomcat
:: Using Windows Explorer or command line
powershell Expand-Archive apache-tomcat-8.5.85-windows-x64.zip -DestinationPath D:\
ren D:\apache-tomcat-8.5.85 apache-tomcat
```

## Memory Configuration

EPMware requires increased memory allocation for optimal performance.

### Create setenv Script

#### Linux setenv.sh

Create `/opt/tomcat/bin/setenv.sh`:
```bash
#!/bin/bash
# EPMware Tomcat Environment Settings

# Memory Settings (Adjust based on available RAM)
export CATALINA_OPTS="$CATALINA_OPTS -Xms8192m"
export CATALINA_OPTS="$CATALINA_OPTS -Xmx12288m"

# Garbage Collection
export CATALINA_OPTS="$CATALINA_OPTS -XX:+UseG1GC"
export CATALINA_OPTS="$CATALINA_OPTS -XX:MaxGCPauseMillis=200"

# JVM Options
export CATALINA_OPTS="$CATALINA_OPTS -server"
export CATALINA_OPTS="$CATALINA_OPTS -XX:+UseStringDeduplication"

# Timezone (adjust as needed)
export CATALINA_OPTS="$CATALINA_OPTS -Duser.timezone=America/New_York"

# EPMware Specific
export CATALINA_OPTS="$CATALINA_OPTS -Depmware.home=/opt/epmware"
```

Make it executable:
```bash
chmod +x /opt/tomcat/bin/setenv.sh
```

#### Windows setenv.bat

Create `D:\apache-tomcat\bin\setenv.bat`:
```batch
@echo off
rem EPMware Tomcat Environment Settings

rem Memory Settings
set "JAVA_OPTS=%JAVA_OPTS% -Xms8192m -Xmx12288m"

rem Garbage Collection  
set "JAVA_OPTS=%JAVA_OPTS% -XX:+UseG1GC"
set "JAVA_OPTS=%JAVA_OPTS% -XX:MaxGCPauseMillis=200"

rem JVM Options
set "JAVA_OPTS=%JAVA_OPTS% -server"
set "JAVA_OPTS=%JAVA_OPTS% -XX:+UseStringDeduplication"

rem Timezone
set "JAVA_OPTS=%JAVA_OPTS% -Duser.timezone=America/New_York"

rem EPMware Specific
set "JAVA_OPTS=%JAVA_OPTS% -Depmware.home=D:\epmware"
```

### Memory Sizing Guidelines

| Environment | Min Heap (-Xms) | Max Heap (-Xmx) | Recommended RAM |
|------------|-----------------|-----------------|-----------------|
| Development | 2GB | 4GB | 8GB |
| Test | 4GB | 8GB | 16GB |
| Production | 8GB | 12GB | 32GB |
| Large Production | 12GB | 24GB | 64GB |

## Server Configuration

### Configure server.xml

Edit the main configuration file:

#### Linux
```bash
vi /opt/tomcat/conf/server.xml
```

#### Windows
```batch
notepad D:\apache-tomcat\conf\server.xml
```

### Key Configuration Changes

#### 1. Configure HTTP Connector

```xml
<Connector port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443"
           maxThreads="200"
           minSpareThreads="10"
           acceptCount="100"
           enableLookups="false"
           compression="on"
           compressionMinSize="2048"
           compressableMimeType="text/html,text/xml,text/plain,text/css,text/javascript,application/javascript,application/json"
           URIEncoding="UTF-8" />
```

#### 2. Configure HTTPS Connector (if using SSL)

```xml
<Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol"
           maxThreads="150" SSLEnabled="true">
    <SSLHostConfig>
        <Certificate certificateKeystoreFile="conf/keystore.jks"
                     certificateKeystorePassword="changeit"
                     type="RSA" />
    </SSLHostConfig>
</Connector>
```

#### 3. Configure AJP Connector (if using Apache httpd)

```xml
<Connector protocol="AJP/1.3"
           address="::1"
           port="8009"
           redirectPort="8443"
           secretRequired="false" />
```

## Context Configuration

Create context configuration for EPMware.

### Create context.xml

Edit `/opt/tomcat/conf/context.xml` (Linux) or `D:\apache-tomcat\conf\context.xml` (Windows):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Context>
    <!-- Default set of monitored resources -->
    <WatchedResource>WEB-INF/web.xml</WatchedResource>
    <WatchedResource>WEB-INF/tomcat-web.xml</WatchedResource>
    <WatchedResource>${catalina.base}/conf/web.xml</WatchedResource>

    <!-- EPMware specific settings -->
    <Resources cachingAllowed="true" cacheMaxSize="100000" />
    
    <!-- Session configuration -->
    <Manager pathname="" />
    
    <!-- JDBC Connection Pool (if not configured in application) -->
    <!--
    <Resource name="jdbc/epmwareDB"
              auth="Container"
              type="javax.sql.DataSource"
              driverClassName="oracle.jdbc.driver.OracleDriver"
              url="jdbc:oracle:thin:@localhost:1521:epm"
              username="ew"
              password="password"
              maxTotal="40"
              maxIdle="10"
              maxWaitMillis="-1"/>
    -->
</Context>
```

## Logging Configuration

### Configure Logging Properties

Edit `/opt/tomcat/conf/logging.properties`:

```properties
# Add EPMware specific logging
epmware.org.apache.juli.AsyncFileHandler.level = INFO
epmware.org.apache.juli.AsyncFileHandler.directory = ${catalina.base}/logs
epmware.org.apache.juli.AsyncFileHandler.prefix = epmware.
epmware.org.apache.juli.AsyncFileHandler.maxDays = 30

# Set log levels
com.epmware.level = INFO
org.apache.catalina.level = INFO
org.apache.tomcat.level = INFO
```

## Security Configuration

### Remove Default Applications

Remove unnecessary default applications for security:

```bash
# Linux
rm -rf /opt/tomcat/webapps/docs
rm -rf /opt/tomcat/webapps/examples
rm -rf /opt/tomcat/webapps/host-manager
rm -rf /opt/tomcat/webapps/manager

# Keep ROOT for default page or remove if not needed
# rm -rf /opt/tomcat/webapps/ROOT
```

Windows:
```batch
rmdir /s /q D:\apache-tomcat\webapps\docs
rmdir /s /q D:\apache-tomcat\webapps\examples
rmdir /s /q D:\apache-tomcat\webapps\host-manager
rmdir /s /q D:\apache-tomcat\webapps\manager
```

### Configure Security Manager (Optional)

For enhanced security, enable the Security Manager:

Edit catalina.sh (Linux) or catalina.bat (Windows):
```bash
# Add to JAVA_OPTS
-Djava.security.manager 
-Djava.security.policy=$CATALINA_BASE/conf/catalina.policy
```

### Set File Permissions

#### Linux Permissions
```bash
# Set restrictive permissions
chmod 600 /opt/tomcat/conf/*
chmod 700 /opt/tomcat/conf
chmod 700 /opt/tomcat/bin
chmod 700 /opt/tomcat/logs
chmod 700 /opt/tomcat/temp
chmod 700 /opt/tomcat/work
```

## Performance Tuning

### JVM Tuning

Add to setenv script:

```bash
# Performance flags
export CATALINA_OPTS="$CATALINA_OPTS -XX:+AlwaysPreTouch"
export CATALINA_OPTS="$CATALINA_OPTS -XX:+DisableExplicitGC"
export CATALINA_OPTS="$CATALINA_OPTS -XX:+UseConcMarkSweepGC"
export CATALINA_OPTS="$CATALINA_OPTS -XX:+CMSParallelRemarkEnabled"

# Heap dump on out of memory
export CATALINA_OPTS="$CATALINA_OPTS -XX:+HeapDumpOnOutOfMemoryError"
export CATALINA_OPTS="$CATALINA_OPTS -XX:HeapDumpPath=/opt/tomcat/logs"
```

### Connector Tuning

Optimize connector settings in server.xml:

```xml
<Connector port="8080" protocol="HTTP/1.1"
           maxThreads="400"
           minSpareThreads="25"
           maxConnections="10000"
           acceptCount="100"
           connectionTimeout="20000"
           keepAliveTimeout="15000"
           maxKeepAliveRequests="100"
           compression="on"
           compressionMinSize="1024"
           compressableMimeType="text/html,text/xml,text/plain,text/css,text/javascript,application/javascript,application/json"
           URIEncoding="UTF-8"
           relaxedQueryChars="[]|{}^&#x5c;&#x60;&quot;&lt;&gt;" />
```

## Service Configuration

### Linux Service Configuration

Create systemd service file `/etc/systemd/system/tomcat.service`:

```ini
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target oracle.service

[Service]
Type=forking

Environment="JAVA_HOME=/usr/lib/jvm/java-8-openjdk"
Environment="CATALINA_HOME=/opt/tomcat"
Environment="CATALINA_BASE=/opt/tomcat"
Environment="CATALINA_PID=/opt/tomcat/temp/tomcat.pid"

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

User=epmware
Group=epmware
UMask=0007
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
```

Enable and start service:
```bash
sudo systemctl daemon-reload
sudo systemctl enable tomcat
sudo systemctl start tomcat
sudo systemctl status tomcat
```

### Windows Service Installation

Install Tomcat as Windows service:

```batch
cd D:\apache-tomcat\bin

:: Install service
service.bat install EPMware

:: Configure service
tomcat8w.exe //ES//EPMware
```

In the service configuration GUI:
1. Set Startup Type: Automatic
2. Configure memory in Java tab
3. Set dependencies if needed

## Verification

### Start Tomcat

#### Linux
```bash
# Using systemctl
sudo systemctl start tomcat

# Or manually
/opt/tomcat/bin/startup.sh
```

#### Windows
```batch
:: Using service
net start EPMware

:: Or manually
D:\apache-tomcat\bin\startup.bat
```

### Verify Startup

1. Check logs:
```bash
# Linux
tail -f /opt/tomcat/logs/catalina.out

# Windows
type D:\apache-tomcat\logs\catalina.out
```

2. Access Tomcat:
```
http://localhost:8080
```

3. Check process:
```bash
# Linux
ps aux | grep tomcat

# Windows
tasklist | findstr tomcat
```

## Troubleshooting

### Common Issues

**Port 8080 already in use**
```bash
# Find process using port
netstat -tulpn | grep 8080  # Linux
netstat -ano | findstr :8080  # Windows

# Change port in server.xml or stop conflicting service
```

**Out of Memory errors**
- Increase heap size in setenv script
- Check for memory leaks
- Enable heap dump analysis

**Slow startup**
- Check for blocking network calls
- Increase startup timeout
- Review deployed applications

**Cannot connect to Tomcat**
- Verify firewall rules
- Check bind address in server.xml
- Ensure Tomcat is running

## Next Steps

After Tomcat installation:

1. If on Windows, proceed to [Windows Components](windows.md)
2. Deploy EPMware application: [EPMware Application](../application/)
3. Configure JDBC connections
4. Set up SSL if required

!!! success "Tomcat Ready"
    Tomcat is now installed and configured for EPMware deployment.

---

© 2025 EPMware, Inc. All rights reserved.
