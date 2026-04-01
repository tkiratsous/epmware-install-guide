# Tomcat Installation

This section covers the installation and configuration of Apache Tomcat for the EPMware application.


### Download Tomcat

Download Apache Tomcat from the official website:

- **URL**: [tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi)
- **Recommended Version**: The latest version supported by EPMWare is 8.5.xx (for example 8.5.66)


### Installation Process

1. Download the zip file (or any other format applicable for the install)
2. Transfer the zip file to the EPMWare application server
3. Unzip the file to target location on the server. For Example: D:\apache


### Configure Apache Settings 

Perform following steps to configure various Tomcat Apache settings. These settings are
documented in RUNNING.txt file located under home directory where Apache is installed.


### Increase Apache Memory

By default, Apache allocates 256MB memory with fresh installation. Ideally 8 GB of
memory should be allocated to EPMWARE. Please perform following steps to increase
minimum / maximum memory allocation for Tomcat Apache process upon startup.

For Windows : 

  - Create a file or edit file setenv.bat file located under Apache home\bin folder.
  - Add the following line in the file (Minimum memory 8GB and Maximum memory 12GB)
  
    `set “JAVA_OPTS=%JAVA_OPTS% -Xms8192m -Xmx12288m”`
   
For Linux : 
  
   - Create a file OR edit file setenv.sh file located under Apache home/bin folder.
   - Add following line in the file (Minimum memory 8BB and Maximum memory 12GB)
   
     `export “JAVA_OPTS="$JAVA_OPTS -Xms8192m -Xmx12288m”`




## Set Envrionment Variables

Set Environment variable for JAVA_HOME or JRE_HOME. Path should point to
home directory where JAVA (JDK or JRE) is installed. These variables are used
to specify location of a Java Runtime Environment or of a Java Development Kit
that is used to start Tomcat. The JRE_HOME variable is used to specify location
of a JRE. The JAVA_HOME variable is used to specify location of a JDK. Using
JAVA_HOME provides access to certain additional startup options that are not
allowed when JRE_HOME is used. If both JRE_HOME and JAVA_HOME are
specified, JRE_HOME is used. For windows, system environment variable can
also be used to set these variables. Other option could be to use “setenv” file to
specify environment variables.

## Next Steps

After Tomcat installation:
[Install EPMware Application](../install_ew/install_epmware_application.md)


