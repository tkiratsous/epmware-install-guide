# Agent Installation

This section covers the installation and configuration of EPMware agents for communication with target applications.

## Overview

EPMware agents facilitate communication between the EPMware application and target EPM systems. Agents handle metadata deployment, status monitoring, and execution of commands on target servers.

## Agent Architecture

### Agent Components

- **Agent Core**: Main processing engine
- **Communication Module**: Handles EPMware server communication
- **Execution Engine**: Runs deployment tasks
- **Monitoring Service**: Tracks job status
- **Queue Manager**: Manages deployment queue

### Agent Types

| Type | Purpose | Deployment Location |
|------|---------|-------------------|
| On-Premise Agent | On-premise EPM applications | Target application server |
| Cloud Agent | Cloud EPM applications | Cloud server or customer server |
| Central Agent | Multiple applications | Dedicated server |
| Embedded Agent | Single application | Same as EPMware server |

## System Requirements

### Hardware Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| CPU | 2 cores | 4 cores |
| Memory | 4 GB | 8 GB |
| Disk Space | 10 GB | 20 GB |
| Network | 100 Mbps | 1 Gbps |

### Software Requirements

- Java 8 or higher
- Python 2.7 or 3.x
- Operating System:
  - Linux (RHEL, Oracle Linux, Ubuntu)
  - Windows Server 2016 or later

## On-Premise Agent Installation

### Download Agent

1. Access EPMware application
2. Navigate to **Configuration → Agents**
3. Click **Download Agent**
4. Select appropriate version:
   - Linux x64
   - Windows x64

### Linux Installation

#### Extract Agent Package

```bash
# Create agent directory
sudo mkdir -p /opt/epmware/agent
cd /opt/epmware/agent

# Extract agent package
tar -xzvf epmware-agent-linux-x64.tar.gz

# Set permissions
sudo chown -R epmware:epmware /opt/epmware/agent
chmod +x /opt/epmware/agent/bin/*.sh
```

#### Configure Agent

Edit agent configuration file:

```bash
vi /opt/epmware/agent/conf/agent.properties
```

Configure properties:

```properties
# EPMware Server Configuration
epmware.server.url=https://epmware.company.com
epmware.server.port=8443
epmware.agent.name=AGENT_PROD_01
epmware.agent.key=generated_key_here

# Agent Settings
agent.home=/opt/epmware/agent
agent.work.dir=/opt/epmware/agent/work
agent.log.dir=/opt/epmware/agent/logs
agent.temp.dir=/opt/epmware/agent/temp

# Communication Settings
communication.protocol=HTTPS
communication.timeout=60000
communication.retry.count=3
communication.heartbeat.interval=30

# Thread Pool Settings
thread.pool.size=10
thread.pool.max=50
```

#### Install as Service

Create systemd service file:

```bash
sudo vi /etc/systemd/system/epmware-agent.service
```

Service configuration:

```ini
[Unit]
Description=EPMware Agent Service
After=network.target

[Service]
Type=forking
User=epmware
Group=epmware
ExecStart=/opt/epmware/agent/bin/agent.sh start
ExecStop=/opt/epmware/agent/bin/agent.sh stop
ExecReload=/opt/epmware/agent/bin/agent.sh restart
PIDFile=/opt/epmware/agent/agent.pid
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

Enable and start service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable epmware-agent
sudo systemctl start epmware-agent
sudo systemctl status epmware-agent
```

### Windows Installation

#### Extract Agent Package

1. Create directory: `D:\epmware\agent`
2. Extract `epmware-agent-windows-x64.zip`
3. Navigate to agent directory

#### Configure Agent

Edit `D:\epmware\agent\conf\agent.properties`:

```properties
# Windows-specific paths
agent.home=D:\\epmware\\agent
agent.work.dir=D:\\epmware\\agent\\work
agent.log.dir=D:\\epmware\\agent\\logs
agent.temp.dir=D:\\epmware\\agent\\temp

# EPMAutomate path (for cloud)
epmautomate.path=D:\\epmware\\epmautomate\\bin
```

#### Install Windows Service

Run as Administrator:

```batch
cd D:\epmware\agent\bin

:: Install service
agent.bat install

:: Configure service
sc config "EPMware Agent" start=auto
sc config "EPMware Agent" depend=OracleService

:: Start service
net start "EPMware Agent"
```

## Cloud Agent Installation

### AWS Cloud Agent

For AWS-hosted agent:

#### Launch EC2 Instance

```bash
# Instance specifications
Type: t3.medium
OS: Amazon Linux 2
Storage: 20 GB
Security Group: Allow HTTPS from EPMware server
```

#### Install Agent

```bash
# Update system
sudo yum update -y

# Install Java
sudo yum install java-1.8.0-openjdk -y

# Install agent (follow Linux installation steps)
```

### Customer-Managed Cloud Agent

For customer-managed servers connecting to cloud EPM:

#### Prerequisites

- Server with internet access
- EPMAutomate installed
- SSL certificates configured
- Proxy configuration (if applicable)

#### EPMAutomate Integration

Configure EPMAutomate location:

```properties
# EPMAutomate Configuration
epmautomate.enabled=true
epmautomate.home=/opt/epmautomate
epmautomate.bin=/opt/epmautomate/bin/epmautomate.sh
epmautomate.timeout=3600
epmautomate.max.concurrent=5
```

## Agent Registration

### Register with EPMware Server

After installation, register the agent:

1. **Generate Registration Key**
   - Login to EPMware
   - Navigate to **Configuration → Agents**
   - Click **Generate Key**
   - Copy registration key

2. **Register Agent**
   
   Linux:
   ```bash
   /opt/epmware/agent/bin/agent.sh register --key=REGISTRATION_KEY
   ```
   
   Windows:
   ```batch
   D:\epmware\agent\bin\agent.bat register --key=REGISTRATION_KEY
   ```

3. **Verify Registration**
   - Check EPMware UI for agent status
   - Status should show "Connected"

## Agent Configuration

### Queue Configuration

Configure deployment queue settings:

```properties
# Queue Settings
queue.enabled=true
queue.max.size=100
queue.poll.interval=30
queue.batch.size=10
queue.priority.enabled=true

# Deployment Settings
deployment.parallel.enabled=false
deployment.max.concurrent=3
deployment.timeout=3600
deployment.retry.on.failure=true
deployment.retry.max.attempts=3
```

### Security Configuration

Configure agent security:

```properties
# Security Settings
security.ssl.enabled=true
security.ssl.keystore=/opt/epmware/agent/certs/keystore.jks
security.ssl.keystore.password=changeit
security.ssl.truststore=/opt/epmware/agent/certs/truststore.jks
security.ssl.truststore.password=changeit

# Authentication
auth.method=TOKEN
auth.token=generated_token_here
auth.token.refresh.interval=3600
```

### Logging Configuration

Configure agent logging:

```properties
# Logging Settings
log.level=INFO
log.file=/opt/epmware/agent/logs/agent.log
log.max.size=100MB
log.max.files=10
log.pattern=%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n

# Debug Logging
debug.enabled=false
debug.deployment=false
debug.communication=false
```

## Agent Monitoring

### Health Check Configuration

```properties
# Health Check Settings
health.check.enabled=true
health.check.interval=60
health.check.url=/health
health.check.timeout=10

# Monitoring Metrics
metrics.enabled=true
metrics.jmx.enabled=true
metrics.jmx.port=9090
```

### Status Monitoring

Monitor agent status:

```bash
# Check agent status
/opt/epmware/agent/bin/agent.sh status

# View agent logs
tail -f /opt/epmware/agent/logs/agent.log

# Check deployment queue
/opt/epmware/agent/bin/agent.sh queue --list
```

## Agent Management

### Start/Stop Agent

Linux:
```bash
# Start agent
sudo systemctl start epmware-agent

# Stop agent
sudo systemctl stop epmware-agent

# Restart agent
sudo systemctl restart epmware-agent
```

Windows:
```batch
:: Start agent
net start "EPMware Agent"

:: Stop agent
net stop "EPMware Agent"

:: Restart agent
net stop "EPMware Agent" && net start "EPMware Agent"
```

### Update Agent

To update an existing agent:

1. Stop agent service
2. Backup current installation
3. Extract new agent version
4. Copy configuration files
5. Start agent service
6. Verify connectivity

```bash
# Backup current agent
cp -r /opt/epmware/agent /opt/epmware/agent.backup

# Stop service
sudo systemctl stop epmware-agent

# Extract new version
tar -xzvf epmware-agent-new.tar.gz

# Copy configuration
cp /opt/epmware/agent.backup/conf/* /opt/epmware/agent/conf/

# Start service
sudo systemctl start epmware-agent
```

## Multiple Agent Configuration

### Load Balancing

Configure multiple agents for load balancing:

```properties
# Load Balancing Configuration
loadbalancer.enabled=true
loadbalancer.algorithm=ROUND_ROBIN
loadbalancer.agents=AGENT_01,AGENT_02,AGENT_03
loadbalancer.health.check=true
loadbalancer.failover.enabled=true
```

### Agent Groups

Configure agent groups for different applications:

```properties
# Agent Group Configuration
agent.group=PLANNING_AGENTS
agent.group.members=AGENT_PLAN_01,AGENT_PLAN_02
agent.group.applications=PLANNING_PROD,PLANNING_TEST
agent.group.priority=HIGH
```

## Troubleshooting

### Common Issues

**Agent Not Starting**
```
Error: Agent service failed to start
Check: Java installation
Verify: Configuration file syntax
Review: Agent logs for errors
```

**Connection Failed**
```
Error: Cannot connect to EPMware server
Check: Network connectivity
Verify: Firewall rules
Test: Server URL accessibility
```

**Registration Failed**
```
Error: Agent registration failed
Check: Registration key validity
Verify: Agent name uniqueness
Review: Server logs
```

**Deployment Failures**
```
Error: Deployment task failed
Check: Target application connectivity
Verify: Credentials
Review: Deployment logs
```

### Debug Mode

Enable debug logging:

```bash
# Edit configuration
vi /opt/epmware/agent/conf/agent.properties

# Enable debug
debug.enabled=true
log.level=DEBUG

# Restart agent
sudo systemctl restart epmware-agent
```

### Log Files

| Log File | Location | Content |
|----------|----------|---------|
| agent.log | agent/logs/ | Main agent operations |
| deployment.log | agent/logs/ | Deployment details |
| communication.log | agent/logs/ | Server communication |
| error.log | agent/logs/ | Error messages |

## Performance Tuning

### Memory Configuration

Configure JVM memory settings:

```bash
# Edit agent startup script
vi /opt/epmware/agent/bin/agent.sh

# Add JVM options
JAVA_OPTS="-Xms512m -Xmx2048m"
JAVA_OPTS="$JAVA_OPTS -XX:+UseG1GC"
JAVA_OPTS="$JAVA_OPTS -XX:MaxGCPauseMillis=200"
```

### Thread Pool Tuning

```properties
# Thread Pool Optimization
thread.core.size=10
thread.max.size=50
thread.queue.capacity=100
thread.keep.alive=60
```

## Best Practices

1. **Installation**
   - Use dedicated service account
   - Install on separate mount point
   - Configure automated startup

2. **Security**
   - Use SSL for communication
   - Rotate authentication tokens
   - Restrict file permissions

3. **Monitoring**
   - Set up health checks
   - Monitor resource usage
   - Alert on failures

4. **Maintenance**
   - Regular log rotation
   - Periodic updates
   - Backup configuration

## Next Steps

After installing agents:

1. Register agents with EPMware
2. Configure target applications
3. Test deployment connectivity
4. Set up monitoring

---

© 2025 EPMware, Inc. All rights reserved.
