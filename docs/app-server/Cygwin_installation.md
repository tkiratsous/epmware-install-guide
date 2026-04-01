# Install CYGWIN


If the EPMware application is installed on a Windows server, install cygwin if it is not
already installed. In addition to this server, Cygwin will need to be installed on all target
servers which have windows o/s and target application are managed by EPMWARE.

Download cygwin from www.cygwin.com and follow instructions on the cygwin site: [Cygwin Installation Link](https://cygwin.com/install.html)

## Install Cygwin

1. Download from above URL and save the setup.exe file to your Desktop
2. Run setup.exe
3. Select defaults:

   - Install from Internet
   - Install Root: C:\cygwin
   - Install for All Users
   
4. Specify a folder for the local package directory that is not the Cygwin root folder,
   for example, C:\cygwin\packages.
5. Specify the connection method. For example, if the host is connected to the
   Internet through a proxy server, specify the proxy server.
6. Select the mirror site from which to download the software

## Windows Service Registration

Register Tomcat as a Windows service using service.bat script.

