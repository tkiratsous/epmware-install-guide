# Configuration Reference

This appendix provides complete configuration file templates and parameter references for all EPMware components.

## JDBC Configuration (fs_custom.properties)

Complete JDBC configuration template:

```properties
#============================================
# EPMware JDBC Configuration
# Location: /webapps/epmware/WEB-INF/classes/fs_custom.properties
#============================================

# Database Connection
jdbc.driver=oracle.jdbc.driver.OracleDriver
jdbc.url=jdbc:oracle:thin:@//localhost:1521/epmware
jdbc.user=ew
jdbc.password=encrypted_password

# Connection Pool Settings
jdbc.maxPoolSize=40
jdbc.minPoolSize=5
jdbc.initialPoolSize=5
jdbc.maxIdleTime=300
jdbc.maxStatements=200
jdbc.preferredTestQuery=select 1 from dual
jdbc.idleConnectionTestPeriod=60

# Advanced Pool Configuration
jdbc.acquireIncrement=5
jdbc.acquireRetryAttempts=3
jdbc.acquireRetryDelay=1000
jdbc.checkoutTimeout=30000
jdbc.numHelperThreads=10
jdbc.unreturnedConnectionTimeout=0
jdbc.debugUnreturnedConnectionStackTraces=false

# Performance Settings
jdbc.statementCacheSize=100
jdbc.maxStatementsPerConnection=10
jdbc.usesTraditionalReflectiveProxies=false
```

## Application Properties

Complete application.properties template:

```properties
#============================================
# EPMware Application Configuration
#============================================

# Application Settings
app.name=EPMware
app.version=6.6
app.environment=PRODUCTION
app.instance.id=${hostname}

# Server Configuration
server.port=8080
server.servlet.context-path=/epmware
server.servlet.session.timeout=30m
server.compression.enabled=true
server.compression.mime-types=text/html,text/xml,text/plain,text/css,text/javascript

# File Upload Settings
spring.servlet.multipart.max-file-size=100MB
spring.servlet.multipart.max-request-size=100MB
upload.temp.dir=/opt/epmware/temp

# Logging Configuration
logging.level.root=INFO
logging.level.com.epmware=DEBUG
logging.file.name=/opt/epmware/logs/epmware.log
logging.file.max-size=100MB
logging.file.max-history=30
logging.pattern.console=%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n
logging.pattern.file=%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n

# Cache Configuration
spring.cache.type=ehcache
spring.cache.ehcache.config=classpath:ehcache.xml

# Scheduler Configuration
scheduler.enabled=true
scheduler.thread.count=10
scheduler.await.termination.seconds=20
scheduler.wait.for.tasks.to.complete=true

# Email Configuration
spring.mail.host=smtp.company.com
spring.mail.port=587
spring.mail.username=epmware@company.com
spring.mail.password=encrypted_password
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true
spring.mail.properties.mail.smtp.starttls.required=true
spring.mail.properties.mail.smtp.connectiontimeout=5000
spring.mail.properties.mail.smtp.timeout=5000
spring.mail.properties.mail.smtp.writetimeout=5000

# Security Configuration
security.require-ssl=false
security.basic.enabled=true
security.sessions=if-required
security.content-security-policy=default-src 'self'

# Actuator Endpoints
management.endpoints.web.exposure.include=health,info,metrics
management.endpoint.health.show-details=when-authorized
management.metrics.export.simple.enabled=true
```

## Tomcat Configuration (server.xml)

Key Tomcat configuration sections:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Server port="8005" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />

  <GlobalNamingResources>
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
  </GlobalNamingResources>

  <Service name="Catalina">
    <!-- HTTP Connector -->
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
               URIEncoding="UTF-8"
               relaxedQueryChars="[]|{}^&#x5c;&#x60;&quot;&lt;&gt;" />

    <!-- HTTPS Connector -->
    <Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol"
               maxThreads="150" SSLEnabled="true">
        <SSLHostConfig>
            <Certificate certificateKeystoreFile="conf/keystore.jks"
                         certificateKeystorePassword="changeit"
                         type="RSA" />
        </SSLHostConfig>
    </Connector>

    <!-- AJP Connector -->
    <Connector protocol="AJP/1.3"
               address="::1"
               port="8009"
               redirectPort="8443"
               secretRequired="false" />

    <Engine name="Catalina" defaultHost="localhost">
      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>

      <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
      </Host>
    </Engine>
  </Service>
</Server>
```

## Agent Configuration (agent.properties)

Complete agent configuration template:

```properties
#============================================
# EPMware Agent Configuration
#============================================

# EPMware Server Configuration
epmware.server.url=https://epmware.company.com
epmware.server.port=8443
epmware.agent.name=AGENT_PROD_01
epmware.agent.key=generated_key_here
epmware.agent.group=PRODUCTION

# Agent Settings
agent.home=/opt/epmware/agent
agent.work.dir=/opt/epmware/agent/work
agent.log.dir=/opt/epmware/agent/logs
agent.temp.dir=/opt/epmware/agent/temp
agent.pid.file=/opt/epmware/agent/agent.pid

# Communication Settings
communication.protocol=HTTPS
communication.timeout=60000
communication.retry.count=3
communication.retry.delay=5000
communication.heartbeat.interval=30
communication.heartbeat.timeout=60

# SSL Configuration
ssl.enabled=true
ssl.keystore.path=/opt/epmware/agent/certs/keystore.jks
ssl.keystore.password=changeit
ssl.keystore.type=JKS
ssl.truststore.path=/opt/epmware/agent/certs/truststore.jks
ssl.truststore.password=changeit
ssl.truststore.type=JKS
ssl.verify.hostname=true

# Thread Pool Configuration
thread.pool.core.size=10
thread.pool.max.size=50
thread.pool.queue.capacity=100
thread.pool.keep.alive.seconds=60

# Queue Configuration
queue.enabled=true
queue.max.size=100
queue.poll.interval.seconds=30
queue.batch.size=10
queue.priority.enabled=true
queue.persistence.enabled=true
queue.persistence.path=/opt/epmware/agent/queue

# Deployment Configuration
deployment.parallel.enabled=false
deployment.max.concurrent=3
deployment.timeout.seconds=3600
deployment.retry.on.failure=true
deployment.retry.max.attempts=3
deployment.retry.delay.seconds=60
deployment.rollback.on.error=true

# EPMAutomate Configuration (for cloud)
epmautomate.enabled=false
epmautomate.home=/opt/epmautomate
epmautomate.bin=/opt/epmautomate/bin/epmautomate.sh
epmautomate.timeout.seconds=3600
epmautomate.max.concurrent=5

# Monitoring Configuration
monitoring.enabled=true
monitoring.metrics.enabled=true
monitoring.metrics.interval.seconds=60
monitoring.health.check.enabled=true
monitoring.health.check.interval.seconds=30
monitoring.jmx.enabled=true
monitoring.jmx.port=9090

# Logging Configuration
logging.level=INFO
logging.file=/opt/epmware/agent/logs/agent.log
logging.max.file.size=100MB
logging.max.files=10
logging.pattern=%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n

# Debug Settings
debug.enabled=false
debug.deployment=false
debug.communication=false
debug.queue=false
```

## Log4j Configuration

Complete log4j.properties template:

```properties
#============================================
# Log4j Configuration for EPMware
#============================================

# Root Logger
log4j.rootLogger=INFO, file, console

# Console Appender
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.Target=System.out
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=%d{HH:mm:ss.SSS} [%t] %-5p %c{1} - %m%n

# File Appender
log4j.appender.file=org.apache.log4j.DailyRollingFileAppender
log4j.appender.file.File=/opt/epmware/logs/epmware.log
log4j.appender.file.DatePattern='.'yyyy-MM-dd
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=%d{ISO8601} [%t] %-5p %c - %m%n

# Error File Appender
log4j.appender.error=org.apache.log4j.DailyRollingFileAppender
log4j.appender.error.File=/opt/epmware/logs/error.log
log4j.appender.error.DatePattern='.'yyyy-MM-dd
log4j.appender.error.Threshold=ERROR
log4j.appender.error.layout=org.apache.log4j.PatternLayout
log4j.appender.error.layout.ConversionPattern=%d{ISO8601} [%t] %-5p %c - %m%n

# Audit Appender
log4j.appender.audit=org.apache.log4j.DailyRollingFileAppender
log4j.appender.audit.File=/opt/epmware/logs/audit.log
log4j.appender.audit.DatePattern='.'yyyy-MM-dd
log4j.appender.audit.layout=org.apache.log4j.PatternLayout
log4j.appender.audit.layout.ConversionPattern=%d{ISO8601} [%t] %c - %m%n

# Application Loggers
log4j.logger.com.epmware=DEBUG, file
log4j.logger.com.epmware.audit=INFO, audit
log4j.logger.com.epmware.security=INFO, audit

# Framework Loggers
log4j.logger.org.springframework=INFO
log4j.logger.org.hibernate=WARN
log4j.logger.org.apache.tomcat=INFO
log4j.logger.org.apache.catalina=INFO

# Database Loggers
log4j.logger.org.hibernate.SQL=WARN
log4j.logger.org.hibernate.type=WARN
log4j.logger.com.mchange.v2.c3p0=INFO

# Suppress specific noisy loggers
log4j.logger.org.apache.catalina.startup.DigesterFactory=ERROR
log4j.logger.org.apache.catalina.util.LifecycleBase=ERROR
log4j.logger.org.apache.coyote.http11.Http11NioProtocol=WARN
log4j.logger.org.apache.sshd.common.util.SecurityUtils=WARN
log4j.logger.org.apache.tomcat.util.net.NioSelectorPool=WARN
```

## Environment Variables

System environment variables template:

```bash
#============================================
# EPMware Environment Variables
# Add to /etc/profile or .bashrc
#============================================

# Java Configuration
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk
export JRE_HOME=$JAVA_HOME/jre
export PATH=$JAVA_HOME/bin:$PATH

# Jython Configuration
export JYTHON_HOME=/opt/jython
export PATH=$JYTHON_HOME/bin:$PATH

# Tomcat Configuration
export CATALINA_HOME=/opt/tomcat
export CATALINA_BASE=/opt/tomcat
export CATALINA_OPTS="-Xms8192m -Xmx12288m"

# Oracle Configuration
export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
export ORACLE_SID=EPMWARE
export PATH=$ORACLE_HOME/bin:$PATH
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$LD_LIBRARY_PATH

# EPM Configuration (if applicable)
export EPM_ORACLE_HOME=/Oracle/Middleware
export EPM_ORACLE_INSTANCE=/Oracle/Middleware/user_projects/epmsystem1

# EPMware Configuration
export EPMWARE_HOME=/opt/epmware
export EPMWARE_CONFIG=/opt/epmware/config
export EPMWARE_LOGS=/opt/epmware/logs

# Network Configuration
export http_proxy=http://proxy.company.com:8080
export https_proxy=http://proxy.company.com:8080
export no_proxy=localhost,127.0.0.1,.company.com

# Locale Configuration
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```

## Security Configuration

Security configuration templates:

### SSL/TLS Configuration

```properties
# SSL Configuration
server.ssl.enabled=true
server.ssl.key-store=/opt/epmware/certs/keystore.p12
server.ssl.key-store-password=changeit
server.ssl.key-store-type=PKCS12
server.ssl.key-alias=epmware
server.ssl.key-password=changeit
server.ssl.trust-store=/opt/epmware/certs/truststore.p12
server.ssl.trust-store-password=changeit
server.ssl.trust-store-type=PKCS12
server.ssl.client-auth=want
server.ssl.ciphers=TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
server.ssl.protocol=TLS
server.ssl.enabled-protocols=TLSv1.2,TLSv1.3
```

### LDAP Configuration

```properties
# LDAP Configuration
ldap.enabled=true
ldap.url=ldap://ldap.company.com:389
ldap.base.dn=dc=company,dc=com
ldap.username=cn=admin,dc=company,dc=com
ldap.password=encrypted_password
ldap.user.search.base=ou=users
ldap.user.search.filter=(uid={0})
ldap.group.search.base=ou=groups
ldap.group.search.filter=(member={0})
ldap.connection.pool.size=10
ldap.connection.pool.max.size=20
ldap.connection.timeout=5000
```

## Performance Tuning Parameters

JVM and system tuning parameters:

```bash
# JVM Tuning Parameters
-server
-Xms8192m
-Xmx12288m
-XX:NewRatio=2
-XX:+UseG1GC
-XX:MaxGCPauseMillis=200
-XX:InitiatingHeapOccupancyPercent=45
-XX:+DisableExplicitGC
-XX:+AlwaysPreTouch
-XX:+ParallelRefProcEnabled
-XX:+UseStringDeduplication
-XX:+UnlockExperimentalVMOptions
-XX:G1NewSizePercent=30
-XX:G1MaxNewSizePercent=40
-XX:G1HeapRegionSize=8M
-XX:G1ReservePercent=20
-XX:G1HeapWastePercent=5
-XX:G1MixedGCCountTarget=4
-XX:G1MixedGCLiveThresholdPercent=90
-XX:G1RSetUpdatingPauseTimePercent=5
-XX:SurvivorRatio=8
-XX:MaxTenuringThreshold=15
-Xloggc:/opt/epmware/logs/gc.log
-XX:+PrintGCDetails
-XX:+PrintGCDateStamps
-XX:+UseGCLogFileRotation
-XX:NumberOfGCLogFiles=10
-XX:GCLogFileSize=10M
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/opt/epmware/dumps/
```

---

© 2025 EPMware, Inc. All rights reserved.
