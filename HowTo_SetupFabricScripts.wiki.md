

## Introduction

This page explains how to install the fabric installation scripts for Bungeni onto your computer.
The assumption here is that you have already [installed fabric](http://code.google.com/p/bungeni-portal/wiki/HowTo_InstallFabric)

**It is advisable to run all these commands as non-root user** .

## Setup

Checkout the fabric scripts into a folder called `fabric` or any name of your choice

```
svn co http://bungeni-portal.googlecode.com/svn/fabric/trunk ./fabric
```


## Configuring

Before starting a build using fabric -- configure your installation as per your requirements as described in [Understanding Fabric ini files](HowTo_ConfigureFabricIni.md).

Most importantly you need to set the **`system_root`** and **`app_host`** parameters.

For example -

  * if you wanted to install Bungeni in the `/home/undesa/testing` folder of your computer - and you wanted to install it as a development build -- by default fabric checks out the source anonymously (over http:// ) when `development_build` is set to true it checks out the source using the user name and password provided in the `[scm]` section -- the checkout is a `https://` checkout. You would edit the following parameters in your  setup.ini file :
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

  * To configure translations for your instance set **enabled\_translations** under **custom**
> > The enabled languages should be colon separated language codes e.g. `en:en_KE:fr_RW`
```
[custom]
folder = deploy_custom
enabled_translations = en
translatable_packages = core:ui:models
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

## Adding a fabricrc file

Sometimes its useful to have a `.fabricrc` file in your home directory.
This stores the shell user name and password, and saves you from entering the user name and password every time you run a fabric action.

To create a fabricrc file -- create a file called **`.fabricrc`** in your home directory (the `~` or `/home/<user>` directory) with the following contents
```
user=enter-your-shell-user-name
password=enter-your-shell-password
```

## Executing fabric actions

Fabric actions must be run in the `fabric` folder which you created in the **Setup** section above.

For example if the fabric scripts were installed into `/home/undesa/fabric` -- you would run the scripts from that folder :

```
cd /home/undesa/fabric
fab --list
```

For more details see [how to run fabric actions](HowTo_RunFabricCommands.md)