# EPMware On-Premise Installation Guide

# Overview

EPMware is a master data management and workflow tool that manages master data and enforces your organization’s workflow around everyday metadata processes.

By configuring shared dimensions in EPMware, users can request metadata once, and the workflow engine routes the request for approvals and deploys it to the participating target systems. This enables standardization and rationalization of metadata as your organization evolves its master data strategy.

## Workflow & Monitoring

The EPMware dashboard allows users, managers, and administrators to monitor metadata requests in real time. Users can track a request from the **Create** stage through **Review**, **Approve**, and **Deploy** stages.

A graphical representation of each request helps identify bottlenecks and determine if escalation is required.


## Integration & Automation

EPMware provides seamless integration with:

- Hyperion EPMA  
- Hyperion Financial Management (HFM)  
- Essbase  
- Planning applications  

This enables automatic metadata deployment without manual intervention or file handling.

Approved metadata can be:

  - Automatically deployed  
  - Scheduled using the built-in scheduler  

## Data Visibility & Security

  - One-click import of target system hierarchies allows users to visualize metadata in production environments  
  - A configurable security module integrates with LDAP or Microsoft Active Directory (MSAD)  

## Workflow Builder

The **Workflow Builder™** enables administrators to design and manage scalable workflows.

Key capabilities include:

  - Task-driven workflows  
  - Reusable workflow components  
  - Rule-based validations  
  - Exception handling  
  - Email notifications across workflow stages  
  - Custom scripting and functions for advanced customization  

## Deployment Management

The EPMware deployment module centrally manages metadata deployments.

Features include:

  - On-demand or scheduled deployments  
  - Batch execution during off-hours  
  - Real-time monitoring of deployments  
  - Calendar-based scheduling (daily, weekly, monthly)  

## Audit & Reporting

EPMware maintains a complete audit trail of all transactions from request to deployment.

- Tracks every transaction, approval, and deployment  
- Provides detailed audit reports  
- Enables querying of historical data through the Audit module  



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

    [:octicons-arrow-right-24: Install database](database/oracle-setup.md)

-   :material-application:{ .lg .middle } **Application Server**

    ---

    Tomcat and prerequisite software setup

    [:octicons-arrow-right-24: Configure server](app-server/Cygwin_installation.md)


</div>

## Support & Resources

- **Technical Support**: support@epmware.com
- **Phone**: 408-614-0442
- **Website**: [www.epmware.com](https://www.epmware.com)

!!! warning "Important Note"
    This guide assumes familiarity with Oracle EPM products, database administration, and web application deployment. Ensure you have appropriate backups before beginning the installation process.

---

© 2025 EPMware, Inc. All rights reserved.
