# Installation Scripts

This appendix contains useful scripts for EPMware installation, configuration, and maintenance.

## Installation Scripts

### Complete Installation Script (Linux)

```bash
#!/bin/bash
# EPMware Complete Installation Script
# Run as root or with sudo

set -e

# Variables
EPMWARE_HOME="/opt/epmware"
TOMCAT_HOME="/opt/tomcat"
ORACLE_SID="EPMWARE"
EPMWARE_USER="epmware"

echo "========================================="
echo "EPMware Installation Script"
echo "========================================="

# Create user and directories
echo "Creating EPMware user and directories..."
groupadd ${EPMWARE_USER}
useradd -g ${EPMWARE_USER} -m -s /bin/bash ${EPMWARE_USER}
mkdir -p ${EPMWARE_HOME}/{app,data,logs,backup,config}
mkdir -p ${EPMWARE_HOME}/data/{archive,stage,temp}
chown -R ${EPMWARE_USER}:${EPMWARE_USER} ${EPMWARE_HOME}

# Install Java
echo "Installing Java..."
yum install -y java-1.8.0-openjdk java-1.8.0-openjdk-devel
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk
echo "export JAVA_HOME=${JAVA_HOME}" >> /etc/profile
echo "export PATH=\$JAVA_HOME/bin:\$PATH" >> /etc/profile

# Install Jython
echo "Installing Jython..."
cd /tmp
wget https://repo1.maven.org/maven2/org/python/jython-installer/2.7.3/jython-installer-2.7.3.jar
java -jar jython-installer-2.7.3.jar -s -d /opt/jython
echo "export JYTHON_HOME=/opt/jython" >> /etc/profile
echo "export PATH=\$JYTHON_HOME/bin:\$PATH" >> /etc/profile

# Install Tomcat
echo "Installing Tomcat..."
cd /tmp
wget https://downloads.apache.org/tomcat/tomcat-8/v8.5.85/bin/apache-tomcat-8.5.85.tar.gz
tar -xzvf apache-tomcat-8.5.85.tar.gz -C /opt/
mv /opt/apache-tomcat-8.5.85 ${TOMCAT_HOME}
chown -R ${EPMWARE_USER}:${EPMWARE_USER} ${TOMCAT_HOME}

# Configure Tomcat memory
cat > ${TOMCAT_HOME}/bin/setenv.sh << EOF
#!/bin/bash
export CATALINA_OPTS="\$CATALINA_OPTS -Xms8192m -Xmx12288m"
export CATALINA_OPTS="\$CATALINA_OPTS -XX:+UseG1GC"
export CATALINA_OPTS="\$CATALINA_OPTS -XX:MaxGCPauseMillis=200"
EOF
chmod +x ${TOMCAT_HOME}/bin/setenv.sh

# Create systemd service for Tomcat
cat > /etc/systemd/system/tomcat.service << EOF
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking
User=${EPMWARE_USER}
Group=${EPMWARE_USER}
Environment="JAVA_HOME=${JAVA_HOME}"
Environment="CATALINA_HOME=${TOMCAT_HOME}"
Environment="CATALINA_BASE=${TOMCAT_HOME}"
Environment="CATALINA_PID=${TOMCAT_HOME}/temp/tomcat.pid"
ExecStart=${TOMCAT_HOME}/bin/startup.sh
ExecStop=${TOMCAT_HOME}/bin/shutdown.sh
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
EOF

# Enable and start Tomcat
systemctl daemon-reload
systemctl enable tomcat

echo "========================================="
echo "Installation complete!"
echo "Next steps:"
echo "1. Configure database"
echo "2. Deploy EPMware WAR file"
echo "3. Configure JDBC connection"
echo "========================================="
```

### Database Setup Script

```sql
-- EPMware Database Setup Script
-- Run as SYS user

-- Create tablespaces
CREATE TABLESPACE EWD 
DATAFILE '/u01/app/oracle/oradata/EPMWARE/EWD.dbf' 
SIZE 1024M AUTOEXTEND ON NEXT 500K MAXSIZE 2048M;

CREATE TABLESPACE EWX 
DATAFILE '/u01/app/oracle/oradata/EPMWARE/EWX.dbf' 
SIZE 10M AUTOEXTEND ON NEXT 500K MAXSIZE 512M;

-- Create user
CREATE USER ew IDENTIFIED BY "EPMware2024!";
ALTER USER ew QUOTA UNLIMITED ON EWD;
ALTER USER ew QUOTA UNLIMITED ON EWX;
ALTER USER ew DEFAULT TABLESPACE EWD;

-- Grant privileges
GRANT CONNECT TO ew;
GRANT CREATE TABLE TO ew;
GRANT CREATE SEQUENCE TO ew;
GRANT CREATE VIEW TO ew;
GRANT CREATE PROCEDURE TO ew;
GRANT CREATE TYPE TO ew;
GRANT CREATE JOB TO ew;
GRANT EXECUTE ON DBMS_CRYPTO TO ew;

-- Create sleep function
CREATE OR REPLACE PROCEDURE sleep(seconds NUMBER)
AS
BEGIN
    DBMS_LOCK.SLEEP(seconds);
END;
/
CREATE OR REPLACE PUBLIC SYNONYM sleep FOR sleep;
GRANT EXECUTE ON sleep TO ew;

-- Create directories
CREATE OR REPLACE DIRECTORY ew_archive_db_dir AS '/ew/db/data/archive';
CREATE OR REPLACE DIRECTORY ew_stage_db_dir AS '/ew/db/data/stage';
CREATE OR REPLACE DIRECTORY ew_temp_db_dir AS '/ew/db/data/temp';
GRANT READ, WRITE ON DIRECTORY ew_archive_db_dir TO ew;
GRANT READ, WRITE ON DIRECTORY ew_stage_db_dir TO ew;
GRANT READ, WRITE ON DIRECTORY ew_temp_db_dir TO ew;

COMMIT;
```

## Maintenance Scripts

### Backup Script

```bash
#!/bin/bash
# EPMware Backup Script

# Variables
BACKUP_DIR="/backup/epmware"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
RETENTION_DAYS=30

# Create backup directory
mkdir -p ${BACKUP_DIR}/${TIMESTAMP}

# Backup database
echo "Backing up database..."
expdp ew/password@EPMWARE \
      directory=DATA_PUMP \
      dumpfile=ew_${TIMESTAMP}.dmp \
      logfile=ew_${TIMESTAMP}.log

# Backup application
echo "Backing up application..."
tar -czvf ${BACKUP_DIR}/${TIMESTAMP}/epmware_app_${TIMESTAMP}.tar.gz \
    /opt/tomcat/webapps/epmware/

# Backup configuration
echo "Backing up configuration..."
tar -czvf ${BACKUP_DIR}/${TIMESTAMP}/epmware_config_${TIMESTAMP}.tar.gz \
    /opt/epmware/config/

# Remove old backups
echo "Removing old backups..."
find ${BACKUP_DIR} -type d -mtime +${RETENTION_DAYS} -exec rm -rf {} \;

echo "Backup completed: ${BACKUP_DIR}/${TIMESTAMP}"
```

### Health Check Script

```bash
#!/bin/bash
# EPMware Health Check Script

# Colors for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color

echo "========================================="
echo "EPMware Health Check"
echo "========================================="

# Check database
echo -n "Database Status: "
sqlplus -s ew/password@EPMWARE << EOF > /dev/null 2>&1
SELECT 1 FROM dual;
EXIT;
EOF
if [ $? -eq 0 ]; then
    echo -e "${GREEN}OK${NC}"
else
    echo -e "${RED}FAILED${NC}"
fi

# Check Tomcat
echo -n "Tomcat Status: "
if systemctl is-active --quiet tomcat; then
    echo -e "${GREEN}Running${NC}"
else
    echo -e "${RED}Stopped${NC}"
fi

# Check EPMware application
echo -n "EPMware Application: "
HTTP_STATUS=$(curl -s -o /dev/null -w "%{http_code}" http://localhost:8080/epmware)
if [ ${HTTP_STATUS} -eq 200 ]; then
    echo -e "${GREEN}Accessible${NC}"
else
    echo -e "${RED}Not Accessible (HTTP ${HTTP_STATUS})${NC}"
fi

# Check disk space
echo -n "Disk Space: "
DISK_USAGE=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')
if [ ${DISK_USAGE} -lt 80 ]; then
    echo -e "${GREEN}${DISK_USAGE}% used${NC}"
elif [ ${DISK_USAGE} -lt 90 ]; then
    echo -e "${YELLOW}${DISK_USAGE}% used${NC}"
else
    echo -e "${RED}${DISK_USAGE}% used${NC}"
fi

# Check memory
echo -n "Memory Usage: "
MEM_USAGE=$(free | awk 'NR==2{printf "%.0f", $3*100/$2}')
if [ ${MEM_USAGE} -lt 80 ]; then
    echo -e "${GREEN}${MEM_USAGE}%${NC}"
elif [ ${MEM_USAGE} -lt 90 ]; then
    echo -e "${YELLOW}${MEM_USAGE}%${NC}"
else
    echo -e "${RED}${MEM_USAGE}%${NC}"
fi

echo "========================================="
```

### Log Rotation Script

```bash
#!/bin/bash
# EPMware Log Rotation Script

# Variables
LOG_DIR="/opt/epmware/logs"
ARCHIVE_DIR="/opt/epmware/logs/archive"
RETENTION_DAYS=90
DATE=$(date +%Y%m%d)

# Create archive directory
mkdir -p ${ARCHIVE_DIR}

# Rotate EPMware logs
find ${LOG_DIR} -name "*.log" -type f -mtime +7 -exec gzip {} \;
find ${LOG_DIR} -name "*.log.gz" -type f -exec mv {} ${ARCHIVE_DIR}/ \;

# Rotate Tomcat logs
find /opt/tomcat/logs -name "*.log" -type f -mtime +7 -exec gzip {} \;
find /opt/tomcat/logs -name "*.txt" -type f -mtime +7 -exec gzip {} \;

# Clean old archives
find ${ARCHIVE_DIR} -name "*.gz" -type f -mtime +${RETENTION_DAYS} -delete

echo "Log rotation completed on ${DATE}"
```

## Deployment Scripts

### Metadata Deployment Script

```bash
#!/bin/bash
# EPMware Metadata Deployment Script

# Variables
EPMWARE_URL="http://localhost:8080/epmware"
API_TOKEN="your-api-token"
DEPLOYMENT_ID=$1

if [ -z "${DEPLOYMENT_ID}" ]; then
    echo "Usage: $0 <deployment_id>"
    exit 1
fi

# Trigger deployment
echo "Starting deployment ${DEPLOYMENT_ID}..."
RESPONSE=$(curl -s -X POST \
    -H "Authorization: Bearer ${API_TOKEN}" \
    -H "Content-Type: application/json" \
    "${EPMWARE_URL}/api/deployment/execute/${DEPLOYMENT_ID}")

# Check status
STATUS=$(echo ${RESPONSE} | jq -r '.status')
if [ "${STATUS}" == "SUCCESS" ]; then
    echo "Deployment started successfully"
else
    echo "Deployment failed: ${RESPONSE}"
    exit 1
fi

# Monitor deployment
while true; do
    STATUS=$(curl -s \
        -H "Authorization: Bearer ${API_TOKEN}" \
        "${EPMWARE_URL}/api/deployment/status/${DEPLOYMENT_ID}" | jq -r '.status')
    
    case ${STATUS} in
        "COMPLETED")
            echo "Deployment completed successfully"
            break
            ;;
        "FAILED")
            echo "Deployment failed"
            exit 1
            ;;
        "IN_PROGRESS")
            echo "Deployment in progress..."
            sleep 10
            ;;
        *)
            echo "Unknown status: ${STATUS}"
            exit 1
            ;;
    esac
done
```

## Monitoring Scripts

### Performance Monitor Script

```python
#!/usr/bin/env python
# EPMware Performance Monitor

import psutil
import requests
import json
import time
from datetime import datetime

# Configuration
EPMWARE_URL = "http://localhost:8080/epmware"
ALERT_EMAIL = "admin@company.com"
CHECK_INTERVAL = 60  # seconds

def check_system_resources():
    """Check system resource usage"""
    metrics = {
        'timestamp': datetime.now().isoformat(),
        'cpu_percent': psutil.cpu_percent(interval=1),
        'memory_percent': psutil.virtual_memory().percent,
        'disk_usage': psutil.disk_usage('/').percent,
        'network_connections': len(psutil.net_connections())
    }
    return metrics

def check_application_health():
    """Check EPMware application health"""
    try:
        response = requests.get(f"{EPMWARE_URL}/health", timeout=5)
        return response.status_code == 200
    except:
        return False

def send_alert(message):
    """Send alert notification"""
    # Implement email or other notification method
    print(f"ALERT: {message}")

def main():
    """Main monitoring loop"""
    while True:
        # Check system resources
        metrics = check_system_resources()
        
        # Check thresholds
        if metrics['cpu_percent'] > 90:
            send_alert(f"High CPU usage: {metrics['cpu_percent']}%")
        
        if metrics['memory_percent'] > 90:
            send_alert(f"High memory usage: {metrics['memory_percent']}%")
        
        if metrics['disk_usage'] > 85:
            send_alert(f"High disk usage: {metrics['disk_usage']}%")
        
        # Check application health
        if not check_application_health():
            send_alert("EPMware application is not responding")
        
        # Log metrics
        with open('/opt/epmware/logs/metrics.log', 'a') as f:
            f.write(json.dumps(metrics) + '\n')
        
        # Wait before next check
        time.sleep(CHECK_INTERVAL)

if __name__ == "__main__":
    main()
```

## Utility Scripts

### Password Encryption Script

```bash
#!/bin/bash
# EPMware Password Encryption Utility

echo "EPMware Password Encryption Utility"
echo "===================================="
read -p "Enter password to encrypt: " -s password
echo

# Encrypt password (example using base64, use proper encryption in production)
encrypted=$(echo -n "$password" | openssl enc -aes-256-cbc -a -salt -pass pass:EPMware2024)

echo "Encrypted password: $encrypted"
echo "Add this to your configuration file"
```

### Certificate Generation Script

```bash
#!/bin/bash
# Generate SSL Certificate for EPMware

# Variables
KEYSTORE_DIR="/opt/epmware/certs"
KEYSTORE_PASS="changeit"
DOMAIN="epmware.company.com"

# Create directory
mkdir -p ${KEYSTORE_DIR}

# Generate self-signed certificate
keytool -genkey -alias epmware \
    -keyalg RSA -keysize 2048 \
    -validity 365 \
    -keystore ${KEYSTORE_DIR}/keystore.jks \
    -storepass ${KEYSTORE_PASS} \
    -dname "CN=${DOMAIN}, OU=IT, O=Company, L=City, ST=State, C=US"

echo "Certificate generated: ${KEYSTORE_DIR}/keystore.jks"
echo "Configure in Tomcat server.xml"
```

---

© 2025 EPMware, Inc. All rights reserved.
