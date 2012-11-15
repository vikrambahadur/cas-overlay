# CAS Maven Overlay
This is a sample Jasig CAS overlay. The build is based on CAS `3.5.1` and cas-addons `1.0.3` 
and features the following:

* Support for JSON service registry with automatic reloads
* Support for EhCache ticket registry with a two-node active-active setup
* Support for Jasypt encryption/decryption of sensitive CAS settings
* Support for Ldap authentication and attribute retrieval/release
* Support for a _fully_ automated build

# Requirements
1. Apache Ant `1.8.2` and above
2. Apache Maven `3.0.4` and above
3. Apache Tomcat 6 or 7
4. Java 6 or 7

# Structure
## Filters
Maven filters which describe different settings specific to the build environment are defined inside the `filters`
directory. The available filters currently are:

* `common.properties`: describes settings common to all build environments
* `node1.properties`: describes settings specific to the first CAS node
* `node2.properties`: describes settings specific to the second CAS node
* `localhost.properties`: describes settings specific to the current localhost dev environment

Please refer to inline comments to understand purpose and intent of the available settings.

## Jasypt
The `lib` folder contains the Jasypt CLI, used by the build process to encrypt/decrypt CAS settings that are specially
decorated. You don't need to modify anything here, except when you possibly want to upgrade to the newer version of
the Jasypt library. 

The current version used is `1.9.0`.

A number of settings are decorated using the Jasypt special `ENC` modifier. You may use the appropriate build target
to provide the encrypted value for the setting. To allow CAS to decrypt the password at runtime, set the environment
variable `CAS_JASYPT_ENC_PASSWORD` to the encryption password. At a minimum, you should provide an encrypted password
for the setting `cas.ldap.manager.psw`:

`cas.ldap.manager.psw = SRls87Kn7W3stoH/98WP6g==`

## Build script
Though the build process is primarily controlled by Apache Maven, the actual script that invokes the Maven build is
done through Apache Ant:

1. `pom.xml`: Apache Maven build descriptor; lists dependencies and versions.
2. `build.xml`: Apache Ant bulid script; exposes various `target`s to invoke maven.

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

# Build

* Set the `CATALINA_HOME` environment variable to point to the tomcat directory
* Set the `CAS_JASYPT_ENC_PASSWORD` environment variable to the encryption password used
* Configure `common.properties`
* Configure the appropriate environment filter (i.e. `localhost.properties`)
* Execute `ant deploy`