CAS Maven Overlay
========
This is a sample Jasig CAS overlay that executes the bulid process using Apache Ant. Various configuration settings of CAS 
are externalized into `.properties` files that are then taken into account by Apache Maven during the build's filtering process.
The build also has support for Jasypt which is a small library used to allow encryption/descryption of CAS-related
credentials, such as that of Ldap, etc. 

The build is based on CAS `3.5.1` and cas-addons `1.0.3`. 
