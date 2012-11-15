# CAS Maven Overlay
This is a sample Jasig CAS overlay. The build is based on CAS `3.5.1` and cas-addons `1.0.3` 
and features the following:

* Support for JSON service registry with automatic reloads
* Support for EhCache ticket registry with a two-node active-active setup
* Support for Jasypt encryption/decryption of sensitive CAS settings
* Support for Ldap authentication and attribute retrieval/release
* Support for environment/tier specific builds through Maven filters
* Support for a _fully_ automated build

# Requirements
1. [Apache Ant](http://ant.apache.org/bindownload.cgi) `1.8.2` and above
2. [Apache Maven](http://maven.apache.org/download.html) `3.0.4` and above
3. [Apache Tomcat](http://tomcat.apache.org/) 6 or 7
4. Java 6 or 7

# Configuration
## Filters
Maven filters which describe different settings specific to the build environment are defined inside the `filters`
directory. The available filters currently are:

* [`common.properties`](https://github.com/mmoayyed/cas-overlay/blob/master/filters/common.properties): describes settings common to all build environments
* [`node1.properties`](https://github.com/mmoayyed/cas-overlay/blob/master/filters/node1.properties): describes settings specific to the first CAS node
* [`node2.properties`](https://github.com/mmoayyed/cas-overlay/blob/master/filters/node2.properties): describes settings specific to the second CAS node
* [`localhost.properties`](https://github.com/mmoayyed/cas-overlay/blob/master/filters/localhost.properties): describes settings specific to the current localhost dev environment

Please refer to inline comments to understand purpose and intent of the available settings.

## Jasypt
The `lib` folder contains the [Jasypt CLI](http://www.jasypt.org/cli.html), used by the build process to encrypt/decrypt CAS settings that are specially
decorated. You don't need to modify anything here, except when you possibly want to upgrade to the newer version of
the Jasypt library. 

The current version used is `1.9.0`.

[A number of settings are decorated](https://github.com/mmoayyed/cas-overlay/blob/master/src/main/webapp/WEB-INF/cas.properties#L114) using the Jasypt special `ENC` modifier. You may use the appropriate build target
to provide the encrypted value for the setting. To allow CAS to decrypt the password at runtime, set the environment
variable `CAS_JASYPT_ENC_PASSWORD` to the encryption password. At a minimum, you should provide an encrypted password
for the setting `cas.ldap.manager.psw`:

`cas.ldap.manager.psw = SRls87Kn7W3stoH/98WP6g==`

## Build script
Though the build process is primarily controlled by Apache Maven, the actual script that invokes the Maven build is
done through Apache Ant:

1. `pom.xml`: Apache Maven build descriptor; lists dependencies and versions.
2. `build.xml`: Apache Ant bulid script; exposes various targets to invoke maven.

To display a list of available build targets, execute `ant -projecthelp` in the overlay directory. The following
targets are currently available:

```xml
 clean            Clean deployed artifacts and logs
 cleanTomcatLogs  Clean tomcat log files
 copy             Copy artifacts over to tomcat
 deploy           Clean, package and deploy artifacts
 help             Prints instructions on how to run the build.
 init             Copy all changes to tomcat and restart the service to deploy.
 jasypt-decrypt   Use Jasypt to decrypt a given string
 jasypt-encrypt   Use Jasypt to encrypt a given string
 jasypt-listalg   Use Jasypt for listing the digest and PBE encryption algorithms available in your JVM
 package          Package src artifacts and prepare for deployment
 startTomcat      Start the tomcat server
 stopTomcat       Stop the tomcat server

Default target: help
```
## Strtucture
The actual CAS Spring configuration are broken apart into the following files:

* [`ehcache-replicated.xml`](https://github.com/mmoayyed/cas-overlay/blob/master/src/main/webapp/WEB-INF/ehcache-replicated.xml): Contains the EhCache ticket replication settings through RMI
* [`attributes-configuration.xml`](https://github.com/mmoayyed/cas-overlay/blob/master/src/main/webapp/WEB-INF/spring-configuration/attributes-configuration.xml): Defines the policy and settings around attribute retrieval
* [`clearpass-configuration.xml`](https://github.com/mmoayyed/cas-overlay/blob/master/src/main/webapp/WEB-INF/spring-configuration/clearpass-configuration.xml): Enables the CAS clearPass extension
* [`ldap-configuration.xml`](https://github.com/mmoayyed/cas-overlay/blob/master/src/main/webapp/WEB-INF/spring-configuration/ldap-configuration.xml): Defines settings that configure Ldap authentication
* [`ticketRegistry.xml`](https://github.com/mmoayyed/cas-overlay/blob/master/src/main/webapp/WEB-INF/spring-configuration/ticketRegistry.xml): Defines the ticket registry implementation that is backed by EhCache
* [`services-configuration.xml`](https://github.com/mmoayyed/cas-overlay/blob/master/src/main/webapp/WEB-INF/spring-configuration/services-configuration.xml): Defines the JSON service registry implementation
* [`service-reg-configuration.xml`](https://github.com/mmoayyed/cas-overlay/blob/master/src/main/webapp/WEB-INF/spring-configuration/service-reg-configuration.xml): Defines the access policy of the service registry UI
* [`propertyFileConfigurer.xml`](https://github.com/mmoayyed/cas-overlay/blob/master/src/main/webapp/WEB-INF/spring-configuration/propertyFileConfigurer.xml): Define a Jasypt enabled `EncryptablePropertyPlaceholderConfigurer`
* [`log4j.xml`](https://github.com/mmoayyed/cas-overlay/blob/master/src/main/webapp/WEB-INF/classes/log4j.xml): Defines CAS logging configuration

Note: You don't need to modify any of the files above, unless you are planning to extend the CAS behavior. Appropriate and relevant settings are abstracted and extracted out to environment filters. 

# Build

* Set the `CATALINA_HOME` environment variable to point to the tomcat directory
* Set the `MAVEN_HOME` and `ANT_HOME` environment variables 
* Set the `CAS_JASYPT_ENC_PASSWORD` environment variable to the encryption password used
* Configure `common.properties`
* Configure the appropriate environment filter (i.e. `localhost.properties`)
* Execute `ant deploy`
* The build will list the available environment filters. It will then ask you for the environment name/id for which 
CAS is to be deployed

*Note:* You should execute the build from the command prompt with suffient privileges. 