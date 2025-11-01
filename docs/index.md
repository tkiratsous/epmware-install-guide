# EPMware On-Premise Installation Guide

Welcome to the EPMware On-Premise Installation Guide. This comprehensive guide provides step-by-step instructions for installing and configuring EPMware in your on-premise environment.

## About This Guide

This guide covers the complete installation process for EPMware version 6.6, including database setup, application server configuration, and integration with various Oracle EPM applications. Whether you're setting up a new environment or upgrading an existing one, this guide provides the detailed instructions you need.

!!! info "Version Information"
    This guide covers EPMware version 6.6, updated October 2022

## What is EPMware?

EPMware is a master data management and workflow tool that manages master data and enforces your organization's workflow around everyday processes that surround metadata changes. By configuring shared dimensions in EPMware, users request metadata once and our workflow engine routes the request to obtain approvals and deploys the metadata to participating target systems.

### Key Features

- **Master Data Management** - Centralized control of metadata across multiple EPM applications
- **Workflow Automation** - Built-in workflow engine for routing and approvals
- **Seamless Integration** - Direct integration with Hyperion EPMA, Classic HFM, Classic Essbase, and Classic Planning
- **Real-time Dashboard** - Monitor request status and identify bottlenecks
- **Audit Trail** - Complete transaction logging from request to deployment
- **Scheduler** - Built-in scheduler for automated deployments

## Installation Overview

The installation process consists of the following major steps:

1. **Review System Requirements** - Ensure your environment meets all hardware and software prerequisites
2. **Install Database** - Set up Oracle database and create EPMware schema
3. **Configure Application Server** - Install and configure Tomcat Apache
4. **Deploy EPMware Application** - Install the EPMware WAR file
5. **Configure Target Applications** - Set up connections to HFM, Planning, and other EPM applications
6. **Install EPMware Agent** - Deploy agents for communication with target systems
7. **Verify Installation** - Test connectivity and validate the setup

## Quick Start Checklist

Before beginning the installation, ensure you have:

- [ ] Oracle Database 11.x or later (Standard or Enterprise Edition)
- [ ] Java 1.8.x or higher installed
- [ ] Latest version of Jython
- [ ] Tomcat Apache 8.0.44 or higher
- [ ] Administrator access to all systems
- [ ] Network connectivity between all components
- [ ] Target EPM applications installed and running

## Supported Configurations

### Target Applications
- Oracle Hyperion Financial Management (HFM) - Classic
- Oracle Hyperion Planning - Classic
- Oracle Essbase - Classic
- Oracle PCMCS/EPBCS - Cloud

### Operating Systems
- **Linux** (Recommended for production)
- **Windows Server 2016** or later

### Databases
- Oracle Database 11.x, 12.x or higher
- Standard Edition or Enterprise Edition

## Documentation Sections

<div class="grid cards" markdown>

-   :material-rocket-launch:{ .lg .middle } **Getting Started**

    ---

    Introduction and installation prerequisites

    [:octicons-arrow-right-24: Get started](getting-started/)

-   :material-server:{ .lg .middle } **System Requirements**

    ---

    Hardware and software specifications

    [:octicons-arrow-right-24: View requirements](requirements/)

-   :material-database:{ .lg .middle } **Database Installation**

    ---

    Oracle database setup and schema creation

    [:octicons-arrow-right-24: Install database](database/)

-   :material-application:{ .lg .middle } **Application Server**

    ---

    Tomcat and prerequisite software setup

    [:octicons-arrow-right-24: Configure server](app-server/)

-   :material-package-variant:{ .lg .middle } **EPMware Application**

    ---

    Deploy and configure the EPMware application

    [:octicons-arrow-right-24: Deploy application](application/)

-   :material-target:{ .lg .middle } **Target Applications**

    ---

    Configure connections to EPM applications

    [:octicons-arrow-right-24: Setup targets](targets/)

-   :material-robot:{ .lg .middle } **Agent Installation**

    ---

    Install and configure EPMware agents

    [:octicons-arrow-right-24: Install agents](agent/)

-   :material-check-circle:{ .lg .middle } **Post-Installation**

    ---

    Verification and initial configuration

    [:octicons-arrow-right-24: Verify setup](post-install/)

-   :material-wrench:{ .lg .middle } **Troubleshooting**

    ---

    Common issues and solutions

    [:octicons-arrow-right-24: Get help](troubleshooting/)

</div>

## Support & Resources

- **Technical Support**: support@epmware.com
- **Phone**: 408-614-0442
- **Website**: [www.epmware.com](https://www.epmware.com)

!!! warning "Important Note"
    This guide assumes familiarity with Oracle EPM products, database administration, and web application deployment. Ensure you have appropriate backups before beginning the installation process.

---

© 2025 EPMware, Inc. All rights reserved.
