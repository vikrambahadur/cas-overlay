CAS Maven Overlay
========
This is a sample Jasig CAS overlay. The build is based on CAS `3.5.1` and cas-addons `1.0.3` 
and features the following:

* Support for JSON service registry with automatic reloads
* Support for EhCache ticket registry with a two-node active-active setup
* Support for encryption/decryption of sensitive CAS settings
* Support for a _fully_ automated build

Structure
========
Maven filters which describe different settings specific to the build environment are defined inside the `filters`
directory. The available filters currently are:

* `common.properties`: describes settings common to all build environments
* `node1.properties`: describes settings specific to the first CAS node
* `node2.properties`: describes settings specific to the second CAS node

Please refer to inline comments to understand purpose and intent of the available settings

Build
========
To run the build