CAS Maven Overlay
========
This is a sample Jasig CAS overlay. The build is based on CAS `3.5.1` and cas-addons `1.0.3` 
and features the following:

* Support for JSON service registry with automatic reloads
* Support for EhCache ticket registry with a two-node active-active setup
* Support for Jasypt encryption/decryption of sensitive CAS settings
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

Please refer to inline comments to understand purpose and intent of the available settings

## Jasypt
The `lib` folder contains the Jasypt CLI, used by the build process to encrypt/decrypt CAS settings that are specially
decorated. You don't need to modify anything here, except when you possibly want to upgrade to the newer version of
the Jasypt library. 

The current version used is `1.9.0`.


# Build
To run the build