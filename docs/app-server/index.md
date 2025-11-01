# Application Server Setup

This section covers the installation and configuration of prerequisites for the EPMware application server, including Java, Jython, and system configuration.

## Overview

The EPMware application server requires several software components to be installed and configured before deploying the application. This includes the Java runtime environment, Jython for scripting support, and various system configurations.

## Prerequisites Checklist

Before beginning application server setup:

- [ ] Operating system installed and updated
- [ ] Network connectivity established
- [ ] Sufficient disk space available (60GB minimum)
- [ ] Administrative/root access available
- [ ] Installation files downloaded:
  - [ ] Java JDK/JRE installer
  - [ ] Jython installer
  - [ ] Tomcat Apache package
  - [ ] Cygwin installer (Windows only)

## Java Installation and Configuration

### Java Requirements

EPMware requires Java 8 or higher:

- **Minimum Version**: Java 1.8.0_171
- **Recommended**: Java 8 latest update or Java 11 LTS
- **Supported**: Oracle JDK or OpenJDK

### Download Java

Download the appropriate Java version:

- **Oracle JDK**: [www.oracle.com/java/technologies/](https://www.oracle.com/java/technologies/)
- **OpenJDK**: [adoptopenjdk.net](https://adoptopenjdk.net/) or [openjdk.java.net](https://openjdk.java.net/)

### Install Java

#### Linux Installation

```bash
# For Oracle JDK (RPM-based systems)
sudo rpm -ivh jdk-8u<version>-linux-x64.rpm

# For Oracle JDK (Debian-based systems)
sudo dpkg -i jdk-8u<version>-linux-x64.deb

# For OpenJDK (RPM-based systems)
sudo yum install java-1.8.0-openjdk java-1.8.0-openjdk-devel

# For OpenJDK (Debian-based systems)
sudo apt-get update
sudo apt-get install openjdk-8-jdk
```

#### Windows Installation

1. Run the Java installer executable
2. Follow the installation wizard
3. Note the installation path (e.g., `C:\Program Files\Java\jdk1.8.0_xxx`)
4. Complete the installation

### Configure Java Environment Variables

#### Linux Configuration

Edit the profile file:
```bash
# Edit system profile
sudo vi /etc/profile

# Add the following lines:
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk
export PATH=$JAVA_HOME/bin:$PATH

# Apply changes
source /etc/profile

# Verify installation
java -version
echo $JAVA_HOME
```

#### Windows Configuration

1. Open System Properties:
   - Right-click "This PC" → Properties → Advanced System Settings
   - Click "Environment Variables"

2. Add JAVA_HOME:
   - Click "New" under System Variables
   - Variable name: `JAVA_HOME`
   - Variable value: `C:\Program Files\Java\jdk1.8.0_xxx`

3. Update PATH:
   - Edit PATH variable
   - Add: `%JAVA_HOME%\bin`

4. Apply changes and verify:
```batch
:: Open new command prompt
java -version
echo %JAVA_HOME%
```

## Jython Installation and Configuration

### Jython Requirements

- **Version**: Jython 2.7.2 or later
- **Type**: Standalone installation recommended
- **Download**: [www.jython.org](http://www.jython.org)

### Install Jython

#### Download Jython

```bash
# Download Jython installer
wget https://repo1.maven.org/maven2/org/python/jython-installer/2.7.3/jython-installer-2.7.3.jar
```

#### Linux Installation

```bash
# Create installation directory
sudo mkdir -p /opt/jython

# Run installer
java -jar jython-installer-2.7.3.jar

# During installation:
# - Select "Standalone" installation type
# - Installation directory: /opt/jython
# - Complete installation

# Verify installation
/opt/jython/bin/jython --version
```

#### Windows Installation

```batch
:: Create installation directory
mkdir D:\jython

:: Run installer
java -jar jython-installer-2.7.3.jar

:: During installation:
:: - Select "Standalone" installation type
:: - Installation directory: D:\jython
:: - Complete installation

:: Verify installation
D:\jython\bin\jython.bat --version
```

### Configure Jython Path

#### Linux Configuration

```bash
# Edit profile
sudo vi /etc/profile

# Add Jython to PATH
export JYTHON_HOME=/opt/jython
export PATH=$JYTHON_HOME/bin:$PATH

# Apply changes
source /etc/profile

# Verify
which jython
jython --version
```

#### Windows Configuration

1. Add to System Environment Variables:
   - Variable name: `JYTHON_HOME`
   - Variable value: `D:\jython`

2. Update PATH:
   - Add: `%JYTHON_HOME%\bin`

3. Verify:
```batch
:: Open new command prompt
jython --version
```

## System Configuration

### Linux System Configuration

#### Update System Limits

Edit `/etc/security/limits.conf`:
```bash
# Add the following lines for the epmware user
epmware soft nofile 65536
epmware hard nofile 65536
epmware soft nproc 16384
epmware hard nproc 16384
```

#### Configure Kernel Parameters

Edit `/etc/sysctl.conf`:
```bash
# Add or update the following
net.core.rmem_max = 134217728
net.core.wmem_max = 134217728
net.ipv4.tcp_rmem = 4096 87380 134217728
net.ipv4.tcp_wmem = 4096 65536 134217728
vm.swappiness = 10

# Apply changes
sudo sysctl -p
```

#### Create EPMware User

```bash
# Create user and group
sudo groupadd epmware
sudo useradd -g epmware -m -s /bin/bash epmware

# Set password
sudo passwd epmware

# Create application directories
sudo mkdir -p /opt/epmware
sudo chown -R epmware:epmware /opt/epmware
```

### Windows System Configuration

#### Configure Windows Firewall

1. Open Windows Firewall with Advanced Security
2. Create Inbound Rules:
   - Port 8080 (HTTP)
   - Port 8443 (HTTPS)
   - Port 8009 (AJP)

#### Configure Windows Services

Prepare for service installation (done after Tomcat installation):
```batch
:: Set service dependencies (after Tomcat service creation)
sc config "EPMwareTomcat" depend= "OracleService"
```

## Directory Structure

Create the standard directory structure for EPMware:

### Linux Directory Structure

```bash
# Create directory structure
sudo mkdir -p /opt/epmware/{app,data,logs,backup,config}
sudo mkdir -p /opt/epmware/data/{archive,stage,temp}

# Set permissions
sudo chown -R epmware:epmware /opt/epmware
sudo chmod -R 755 /opt/epmware
```

### Windows Directory Structure

```batch
:: Create directory structure
mkdir D:\epmware
mkdir D:\epmware\app
mkdir D:\epmware\data
mkdir D:\epmware\logs
mkdir D:\epmware\backup
mkdir D:\epmware\config
mkdir D:\epmware\data\archive
mkdir D:\epmware\data\stage
mkdir D:\epmware\data\temp
```

## Environment Verification

### Create Verification Script

Create a script to verify all prerequisites:

#### Linux Verification Script

Create `/opt/epmware/verify_setup.sh`:
```bash
#!/bin/bash

echo "========================================="
echo "EPMware Prerequisites Verification"
echo "========================================="

# Check Java
echo "Checking Java..."
if command -v java &> /dev/null; then
    java -version
    echo "JAVA_HOME: $JAVA_HOME"
else
    echo "ERROR: Java not found"
fi

# Check Jython
echo ""
echo "Checking Jython..."
if command -v jython &> /dev/null; then
    jython --version
    echo "JYTHON_HOME: $JYTHON_HOME"
else
    echo "ERROR: Jython not found"
fi

# Check disk space
echo ""
echo "Checking disk space..."
df -h /opt/epmware

# Check memory
echo ""
echo "Checking memory..."
free -h

# Check network
echo ""
echo "Checking network connectivity..."
ping -c 1 www.google.com &> /dev/null
if [ $? -eq 0 ]; then
    echo "Internet connectivity: OK"
else
    echo "WARNING: No internet connectivity"
fi

echo ""
echo "========================================="
echo "Verification Complete"
echo "========================================="
```

Make it executable:
```bash
chmod +x /opt/epmware/verify_setup.sh
./opt/epmware/verify_setup.sh
```

#### Windows Verification Script

Create `D:\epmware\verify_setup.bat`:
```batch
@echo off
echo =========================================
echo EPMware Prerequisites Verification
echo =========================================

echo.
echo Checking Java...
java -version 2>&1
echo JAVA_HOME: %JAVA_HOME%

echo.
echo Checking Jython...
jython --version 2>&1
echo JYTHON_HOME: %JYTHON_HOME%

echo.
echo Checking disk space...
wmic logicaldisk get size,freespace,caption

echo.
echo Checking memory...
wmic computersystem get TotalPhysicalMemory

echo.
echo =========================================
echo Verification Complete
echo =========================================
pause
```

## Security Configuration

### Linux Security

#### SELinux Configuration (RHEL/CentOS)

```bash
# Check SELinux status
sestatus

# If needed, set to permissive for EPMware
sudo semanage permissive -a httpd_t

# Or disable SELinux (requires reboot)
sudo vi /etc/selinux/config
# Set SELINUX=disabled
```

#### Firewall Configuration

```bash
# For firewalld (RHEL/CentOS 7+)
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --permanent --add-port=8443/tcp
sudo firewall-cmd --reload

# For iptables
sudo iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 8443 -j ACCEPT
sudo service iptables save
```

### Windows Security

#### Configure Antivirus Exclusions

Add exclusions for:
- `D:\epmware\*`
- `D:\apache-tomcat\*`
- Java process
- Tomcat process

## Performance Optimization

### Linux Performance Tuning

```bash
# Optimize TCP settings
echo "net.ipv4.tcp_fin_timeout = 30" >> /etc/sysctl.conf
echo "net.ipv4.tcp_keepalive_time = 1200" >> /etc/sysctl.conf
echo "net.ipv4.tcp_keepalive_probes = 5" >> /etc/sysctl.conf
echo "net.ipv4.tcp_keepalive_intvl = 30" >> /etc/sysctl.conf

# Apply changes
sysctl -p
```

### Windows Performance Tuning

```batch
:: Optimize TCP settings
netsh int tcp set global autotuninglevel=normal
netsh int tcp set global chimney=enabled
netsh int tcp set global rss=enabled
```

## Troubleshooting

### Common Issues

**Java not found**
- Verify JAVA_HOME is set correctly
- Ensure Java bin is in PATH
- Restart terminal/command prompt after changes

**Jython not found**
- Check Jython installation directory
- Verify PATH includes Jython bin
- Test with full path to jython executable

**Permission denied (Linux)**
- Check file ownership
- Verify user has necessary permissions
- Use sudo for system-level operations

**Environment variables not working (Windows)**
- Restart command prompt after changes
- Check for typos in variable names
- Verify paths don't contain spaces (or use quotes)

## Next Steps

After completing the application server prerequisites:

1. Proceed to [Tomcat Configuration](tomcat.md)
2. Install Windows components if applicable: [Windows Components](windows.md)
3. Document all configuration changes
4. Take a system snapshot/backup

!!! success "Prerequisites Complete"
    Once Java and Jython are installed and configured, you're ready to proceed with Tomcat installation.

---

© 2025 EPMware, Inc. All rights reserved.
