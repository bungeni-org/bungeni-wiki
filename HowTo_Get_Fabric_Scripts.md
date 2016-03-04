

## Introduction

This page explains how to install the fabric installation scripts onto your computer.
The assumption here is that you have already [installed fabric](http://code.google.com/p/bungeni-portal/wiki/HowTo_InstallFabric)

## Setup

Checkout the fabric scripts into a folder called `fabric` or any name of your choice

```
svn co http://bungeni-portal.googlecode.com/svn/fabric/trunk ./fabric
```


## Configuring

Before starting a build using fabric -- configure your installation as per your requirements as described in [Understanding Fabric ini files](Understanding_Fabric_ini_Files.md)

For example -

  * if you wanted to install Bungeni in the `/home/undesa/testing` folder of your computer - and you wanted to install it as a development build, you would edit the following parameters in your  bungeni.ini file :
```
[global]
system_root = /home/undesa/testing
development_build = True
local_cache = False
app_host = localhost

[scm]
user=user.name
pass=svnpassword
```


  * If you did not want to install it in development mode :
```
[global]
system_root = /home/undesa/testing
development_build = False
local_cache = False
app_host = localhost

[scm]
user=
pass=
```

  * If you had a linux distro with a non-standard list of required packages :
```
[global]
system_root = /home/undesa/testing
development_build = False
local_cache = False
app_host = localhost
distro_override = CustomDistro:Packages

[scm]
user=
pass=
```

and your distro.ini would look like this :

```
[CustomDistro]
Packages = 
	   package-zip-dev
	   package-jpeg-dev
	   ....
```




