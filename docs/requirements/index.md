# System Requirements

This section details the hardware, software, and network requirements for EPMware on-premise installation.

## Hardware Requirements

### Application Server Recommendations

The EPMware application server hosts the main EPMware application and requires the following specifications:

| Component | Minimum Requirements | Recommended |
|-----------|---------------------|-------------|
| **Processor** | 2 CPU x64 Intel/AMD | 4+ CPU x64 Intel/AMD |
| **Memory (RAM)** | 16 GB | 32 GB or higher |
| **Hard Disk Space** | 60 GB | 100 GB+ SSD |
| **Network** | 1 Gbps | 10 Gbps |

**Performance Considerations:**
- Additional CPUs improve concurrent request processing
- More RAM allows for larger data processing and caching
- SSD storage recommended for improved I/O performance
- Network speed impacts metadata deployment times

### Database Server Recommendations

The database server hosts the EPMware repository and requires:

| Component | Minimum Requirements | Recommended |
|-----------|---------------------|-------------|
| **Processor** | 4 CPU x64 Intel/AMD | 8+ CPU x64 Intel/AMD |
| **Memory (RAM)** | 32 GB | 64 GB or higher |
| **Hard Disk Space** | 60 GB | 200 GB+ SSD |
| **Network** | 1 Gbps | 10 Gbps |

**Sizing Guidelines:**
- Database size grows with metadata complexity and history
- Plan for 3-5 years of data growth
- Consider backup and archive storage requirements
- High-speed storage critical for database performance

### Client Workstation Requirements

End users access EPMware through web browsers with these minimum requirements:

| Component | Requirements |
|-----------|-------------|
| **Operating System** | Windows 8/10/11, macOS, Linux |
| **Web Browser** | Chrome (recommended), Firefox, Edge, Safari |
| **Screen Resolution** | 1920x1080 minimum |
| **Network** | Broadband internet connection |

## Software Requirements

### Operating Systems

**Application Server Supported OS:**
- **Linux (Recommended for Production)**
  - Red Hat Enterprise Linux 7.x, 8.x
  - Oracle Linux 7.x, 8.x
  - CentOS 7.x, 8.x
  - Ubuntu Server 18.04 LTS, 20.04 LTS

- **Windows Server**
  - Windows Server 2016
  - Windows Server 2019
  - Windows Server 2022

**Database Server Supported OS:**
- Same as Application Server
- Linux recommended for production databases

### Database Software

**Oracle Database Versions:**
- Oracle Database 11g Release 2 (11.2.0.4)
- Oracle Database 12c Release 1 & 2
- Oracle Database 19c (recommended)
- Oracle Database 21c

**Database Editions:**
- Standard Edition
- Standard Edition One
- Standard Edition Two
- Enterprise Edition (recommended for production)

**Required Database Features:**
- Partitioning (optional but recommended)
- Advanced Security (optional)
- Real Application Clusters - RAC (optional for HA)

### Java Requirements

**Java Development Kit (JDK) or Runtime Environment (JRE):**
- Java 8 (1.8.x) - Minimum version 1.8.0_171
- Java 11 - Supported
- OpenJDK or Oracle JDK

**Configuration:**
- JAVA_HOME environment variable must be set
- Java bin directory must be in system PATH

### Jython Requirements

**Version:**
- Jython 2.7.2 or later (latest stable version recommended)

**Installation:**
- Download from [www.jython.org](http://www.jython.org)
- Standalone installation type recommended

**Configuration:**
- Jython bin folder must be in system PATH
- On Windows: Add to system environment variables

### Web/Application Server

**Apache Tomcat Versions:**
- Tomcat 8.0.44 (minimum)
- Tomcat 8.5.x (recommended)
- Tomcat 9.0.x (supported)

**Tomcat Requirements:**
- Minimum heap size: 8 GB
- Maximum heap size: 12 GB
- HTTP/HTTPS connectors configured

### Additional Software for Windows

**Cygwin (Windows only):**
- Required for Windows installations
- Latest stable version
- Base packages plus SSH utilities

**Windows Service Wrapper:**
- For running Tomcat as Windows service
- Included with Tomcat installation

## Network Requirements

### Port Requirements

Ensure the following ports are open and available:

| Service | Default Port | Protocol | Direction | Notes |
|---------|-------------|----------|-----------|-------|
| EPMware Application | 8080 | HTTP | Inbound | Can be configured |
| EPMware Application (SSL) | 8443 | HTTPS | Inbound | Recommended for production |
| Oracle Database | 1521 | TCP | Bidirectional | Default Oracle port |
| EPM Applications | Various | HTTP/HTTPS | Outbound | Target application specific |
| SMTP Server | 25/587 | TCP | Outbound | For email notifications |
| LDAP/Active Directory | 389/636 | TCP/LDAPS | Outbound | For authentication |

### Network Connectivity Requirements

**Between Components:**
- Application Server ↔ Database Server: < 1ms latency recommended
- Application Server ↔ Target EPM Apps: < 10ms latency
- Application Server ↔ EPMware Agents: Persistent connection
- Client Browsers ↔ Application Server: < 100ms latency

**Bandwidth Requirements:**
- Minimum: 100 Mbps between servers
- Recommended: 1 Gbps or higher
- Consider metadata file sizes for deployment

### Firewall Configuration

Configure firewall rules to allow:

1. **Inbound to Application Server:**
   - HTTP/HTTPS from client subnets
   - Agent communication ports

2. **Outbound from Application Server:**
   - Database connection
   - Target EPM applications
   - SMTP server
   - LDAP/AD servers

3. **Database Server:**
   - Accept connections from Application Server
   - Backup network access if required

## Target Application Requirements

### Oracle Hyperion Planning (Classic)

**Supported Versions:**
- Planning 11.1.2.4.x
- Planning 11.2.x

**Requirements:**
- LCM Administrator role for EPMware user
- Password file generation capability
- EPM instance accessible via HTTP/HTTPS

### Oracle Hyperion Financial Management (Classic)

**Supported Versions:**
- HFM 11.1.2.4.x
- HFM 11.2.x

**Requirements:**
- HFM administrator access
- reg.properties file access
- Windows-based HFM server requires Cygwin

### Oracle PCMCS/EPBCS (Cloud)

**Supported Versions:**
- All current PCMCS versions
- All current EPBCS versions

**Requirements:**
- EPMAutomate utility
- Service Administrator role
- Internet connectivity
- Valid SSL certificates

### Oracle Essbase (Classic)

**Supported Versions:**
- Essbase 11.1.2.4.x
- Essbase 19c
- Essbase 21c

**Requirements:**
- Essbase administrator access
- MaxL script execution capability

## Capacity Planning

### Small Environment (< 100 users)
- Single application server
- Single database server
- 1-2 EPMware agents
- Basic specifications sufficient

### Medium Environment (100-500 users)
- Single application server (higher specs)
- Dedicated database server
- 2-4 EPMware agents
- Consider load balancing

### Large Environment (> 500 users)
- Multiple application servers (clustered)
- Database with RAC/Data Guard
- 4+ EPMware agents
- Load balancer required
- Consider CDN for static content

## Virtualization Support

EPMware supports installation on virtualized environments:

**Supported Platforms:**
- VMware vSphere 6.x, 7.x
- Microsoft Hyper-V
- Oracle VM
- AWS EC2 instances
- Azure Virtual Machines
- Google Cloud Platform VMs

**Best Practices:**
- Reserve CPU and memory resources
- Use thick provisioned disks
- Enable CPU/Memory hot-add
- Configure anti-affinity rules for HA
- Regular snapshot schedule

## High Availability Considerations

For production environments requiring high availability:

**Application Tier:**
- Multiple Tomcat instances
- Hardware/Software load balancer
- Sticky sessions configured
- Shared file system for artifacts

**Database Tier:**
- Oracle RAC for active-active clustering
- Data Guard for disaster recovery
- Regular RMAN backups
- Standby database recommended

**Agent Tier:**
- Deploy redundant agents
- Automatic failover configuration
- Health monitoring

!!! warning "Important Notes"
    - These are minimum requirements; production environments may need higher specifications
    - Performance depends on metadata complexity and user concurrency
    - Regular monitoring and capacity reviews recommended
    - Contact EPMware Support for sizing assistance for large deployments

## Next Steps

After confirming your environment meets these requirements:

1. Proceed to [Database Installation](../database/)
2. Prepare installation media and documentation
3. Schedule maintenance window if upgrading

---

© 2025 EPMware, Inc. All rights reserved.
