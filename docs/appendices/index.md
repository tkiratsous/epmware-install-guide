# Appendices

This section contains reference materials, scripts, configuration templates, and additional resources for EPMware installation and administration.

## Available Appendices

### [Appendix A - Installation Scripts](scripts.md)
Collection of useful scripts for installation, configuration, and maintenance tasks.

### [Appendix B - Configuration Reference](configuration.md)
Complete configuration file templates and parameter references for all EPMware components.

### [Appendix C - Migration Guide](migration.md)
Procedures for migrating from previous EPMware versions and upgrading existing installations.

## Quick Reference

### Important Directories

| Component | Linux Path | Windows Path |
|-----------|------------|--------------|
| EPMware Home | /opt/epmware | D:\epmware |
| Tomcat Home | /opt/tomcat | D:\apache-tomcat |
| Agent Home | /opt/epmware/agent | D:\epmware\agent |
| Database Files | /u01/app/oracle | D:\app\oracle |
| Log Files | /opt/epmware/logs | D:\epmware\logs |

### Default Ports

| Service | Port | Protocol | Component |
|---------|------|----------|-----------|
| Database | 1521 | TCP | Oracle |
| Application | 8080 | HTTP | Tomcat |
| Application SSL | 8443 | HTTPS | Tomcat |
| Agent | 9090 | TCP | EPMware Agent |
| JMX | 9091 | TCP | Monitoring |

### Environment Variables

| Variable | Purpose | Example |
|----------|---------|---------|
| JAVA_HOME | Java installation | /usr/lib/jvm/java-8 |
| JRE_HOME | JRE location | /usr/lib/jvm/java-8/jre |
| CATALINA_HOME | Tomcat installation | /opt/tomcat |
| ORACLE_HOME | Oracle installation | /u01/app/oracle/product/19.0.0 |
| EPM_ORACLE_HOME | EPM installation | /Oracle/Middleware |

---

© 2025 EPMware, Inc. All rights reserved.
