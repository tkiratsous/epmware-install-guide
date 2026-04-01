# Set JDBC Properties

Stop the Apache service and navigate to `<APACHE_HOME>\webapps\epmware\WEBINF\classes` folder. 

The folder will be created only after placing the `epmware.war` file in the folder and starting Apache.

Modify `fs_custom.properties` file for JDBC properties:


```
#JDBC
jdbc.driver=oracle.jdbc.driver.OracleDriver
jdbc.url=jdbc:oracle:thin:ew@//localhost:1521/epm
jdbc.user=ew
jdbc.password=ew123
jdbc.maxPoolSize=40
jdbc.minPoolSize=5
jdbc.initialPoolSize=5
jdbc.maxIdleTime=300
jdbc.maxStatements=200
jdbc.preferredTestQuery=select 1
jdbc.idleConnectionTestPeriod=60

```

After modifying this file, restart the apache server.


## Next Steps


 Proceed to [Global settings](../epmware_app_settings/global_settings.md)


---
