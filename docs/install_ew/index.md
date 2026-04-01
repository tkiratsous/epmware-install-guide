# Hardware And Software Requirements

This section details the hardware, software, and network requirements for EPMware on-premise installation.



## Application Server Recommendations

The EPMware application server hosts the main EPMware application and requires the following specifications:


| Category                | Details                                                                 |
|------------------------|-------------------------------------------------------------------------|
| Supported Operating Systems | • Linux (Recommended)<br>• Windows Server 2016 or later              |
| Recommended Hardware   | • 2 CPU x64 Intel/AMD64 Processor<br>• 16 GB RAM or higher             |
| Hard Disk Space        | 60 GB                                                                  |
| Required Software      | • Java 1.8x or higher<br>• Jython<br>• Apache Web Server<br>• Tomcat 8.0.44 or higher |
| Recommended Network    | 10 Gb                                                                  |


## Database Server Recommendations

The database server hosts the EPMware repository and requires:

| Category                     | Details                                                                 |
|------------------------------|-------------------------------------------------------------------------|
| Supported Operating Systems  | • Linux (Recommended)<br>• Windows Server 2016 or later                 |
| Recommended Hardware         | • 4 CPU x64 Intel/AMD64 Processor<br>• 32 GB RAM or higher             |
| Hard Disk Space              | 60 GB                                                                  |
| Required Software            | • Oracle Database Standard Edition or higher<br>• Version 12.x or higher |
| Recommended Network          | 10 Gbps                                                                |

**Sizing Guidelines:**

- Database size grows with metadata complexity and history
- Plan for 3-5 years of data growth
- Consider backup and archive storage requirements
- High-speed storage critical for database performance

## Client Workstation Requirements

End users access EPMware through web browsers with these minimum requirements:

| Category                    | Details                                                                 |
|-----------------------------|-------------------------------------------------------------------------|
| Supported Operating Systems | • Windows 8<br>• Windows 10<br>• Linux                                  |
| Web Browser                 | • Chrome (Recommended)<br>• Firefox<br>• Internet Explorer 11.x or higher |


## Next Steps

After confirming your environment meets these requirements:

1. Proceed to [Database Installation](../database/oracle-setup.md)
2. Prepare installation media and documentation
3. Schedule maintenance window if upgrading

---

© 2025 EPMware, Inc. All rights reserved.
