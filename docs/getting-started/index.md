# Getting Started

This section provides an introduction to EPMware and guides you through the initial preparation steps for installation.

## Introduction

EPMware is a comprehensive master data management and workflow solution designed to streamline metadata management across your Oracle EPM landscape. By centralizing metadata requests and automating deployment processes, EPMware ensures consistency and control while reducing manual effort and errors.

### Core Capabilities

**Master Data Management**
- Centralized metadata repository
- Standardization and rationalization of metadata
- Shared dimensions across applications
- Version control and change tracking

**Workflow Automation**
- Configurable approval workflows
- Rule-based validations
- Exception handling
- Email notifications at all stages
- Custom functions and scripting

**Application Integration**
- Seamless integration with Hyperion EPMA
- Direct deployment to Classic HFM
- Classic Essbase support
- Classic Planning automation
- Cloud application support (PCMCS/EPBCS)

**Monitoring & Audit**
- Real-time dashboard
- Graphical workflow visualization
- Complete audit trail
- Deployment metrics
- Performance monitoring

## Purpose

The purpose of this installation guide is to provide comprehensive instructions for installing and configuring EPMware on-premise components:

- Oracle Database setup and configuration
- Prerequisite software installation
- EPMware database objects deployment
- Application server configuration
- EPMware on-premise agent installation
- Target application integration

This guide is intended for:
- System administrators
- Database administrators
- EPM application administrators
- IT infrastructure teams

## Prerequisites Overview

Before beginning the EPMware installation, ensure you have completed the following prerequisite tasks:

### Software Prerequisites

1. **Oracle Database**
   - Version 11.x or later (Standard or Enterprise Edition)
   - Database must be installed and running
   - SYS user access required

2. **Java Runtime Environment**
   - Java 1.8.x or higher
   - JAVA_HOME environment variable configured
   - Java added to system PATH

3. **Jython**
   - Latest version from [www.jython.org](http://www.jython.org)
   - Jython bin folder added to system PATH
   - On Windows: added as system environment variable

4. **Tomcat Apache**
   - Version 8.0.44 or higher
   - Will be installed as part of this guide

### Access Requirements

- **Database Access**
  - SYS user credentials
  - Ability to create tablespaces and schemas
  - Grant privileges capability

- **Server Access**
  - Administrator/root access to application server
  - Ability to install software
  - Modify system environment variables
  - Create Windows services (for Windows installations)

- **Network Access**
  - Connectivity between application server and database
  - Access to target EPM applications
  - Firewall rules configured for required ports

### Target Application Requirements

If integrating with Oracle EPM applications, ensure:

- Target applications are installed and operational
- Administrative credentials available
- LCM Administrator role (for Planning applications)
- Network connectivity established

## Installation Checklist

Use this checklist to track your installation progress:

### Pre-Installation
- [ ] Review system requirements
- [ ] Verify hardware specifications meet minimums
- [ ] Confirm supported operating system version
- [ ] Check available disk space
- [ ] Document network topology
- [ ] Obtain necessary licenses

### Database Preparation
- [ ] Oracle database installed
- [ ] Database running and accessible
- [ ] SYS user credentials available
- [ ] Backup existing databases (if applicable)
- [ ] Plan tablespace sizing
- [ ] Determine tablespace locations

### Software Dependencies
- [ ] Java 1.8+ installed
- [ ] JAVA_HOME configured
- [ ] Jython installed
- [ ] Jython in system PATH
- [ ] Download Tomcat Apache 8.0.44+
- [ ] Download EPMware installation files

### Target Applications
- [ ] List all target EPM applications
- [ ] Verify application versions are supported
- [ ] Obtain administrative credentials
- [ ] Test connectivity to each application
- [ ] Document application URLs/connection details

### Installation Files
- [ ] EPMware WAR file (epmware.war)
- [ ] Database installation scripts (ew_db_install.zip)
- [ ] EPMware agent installation files
- [ ] License file (if applicable)

### Documentation
- [ ] This installation guide
- [ ] Target application documentation
- [ ] Network diagram
- [ ] Credential list (secured)
- [ ] Support contact information

## Installation Sequence

Follow this recommended sequence for installation:

1. **Database Setup**
   - Create tablespaces
   - Create schema
   - Grant privileges
   - Install database objects

2. **Application Server**
   - Install prerequisite software
   - Install and configure Tomcat
   - Configure memory settings
   - Set environment variables

3. **EPMware Application**
   - Deploy WAR file
   - Configure JDBC connections
   - Set application properties
   - Configure email settings

4. **Windows Components** (if applicable)
   - Install Cygwin
   - Register Windows services

5. **Target Applications**
   - Configure each target application
   - Generate password files (Planning)
   - Set up reg.properties (HFM)
   - Install EPMAutomate (Cloud)

6. **Agent Installation**
   - Deploy agents to required servers
   - Configure agent properties
   - Test agent connectivity

7. **Verification**
   - Test database connectivity
   - Verify application startup
   - Validate target connections
   - Perform test deployment

!!! tip "Best Practices"
    - Always backup existing systems before installation
    - Document all configuration changes
    - Test in a non-production environment first
    - Keep installation logs for troubleshooting
    - Follow your organization's change management procedures

## Next Steps

Once you have reviewed this getting started section and completed the prerequisites:

1. Review the [System Requirements](../requirements/) in detail
2. Begin with [Database Installation](../database/)
3. Follow the installation sequence outlined above
4. Refer to [Troubleshooting](../troubleshooting/) if you encounter issues

!!! info "Support"
    If you need assistance during installation, contact EPMware Support:
    - Email: support@epmware.com
    - Phone: 408-614-0442

---

© 2025 EPMware, Inc. All rights reserved.
