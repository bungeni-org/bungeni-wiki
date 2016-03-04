# This page is deprecated please refer to [this page](http://code.google.com/p/bungeni-portal/wiki/Install_BungeniApplicationStack) instead

# Introduction

The Bungeni System is composed of 3 different applications --
  * Bungeni Parliamentary Information System
  * Plone Content Management System
  * Deliverance application proxy (this is used to present a unified interface into both the Bungeni PIS and Plone to the user )

The installation instructions here describe how to install all three.

The recommended way to install the full application stack is to use the capistrano installation scripts.

  1. **Installing Bungeni** -- Start here: SettingUpBungeniInstallationEnvironment and then do this: DeployingBungeniBuildoutWithCapistrano
  1. **Installing Plone** -- Follow these instructions : [Installing Plone](http://code.google.com/p/bungeni-portal/wiki/InstallingPloneForBungeni#Automated_Install_(using_capistrano))
  1. **Installing Deliverance Portal** -- Requires a working capistrano installation of Bungeni. From the capistrano folder run the following command if installing from local package index:
```
cap portal_install:full_from_cache 
```

For using the internet package index :
```
cap portal_install:full
```

Supervisor (running on localhost port 8888) is used to control these services.
Additional the postgresql service is also controlled via supervisor.


(below is the new structure to supercede the above -- work in progress )
# Installation Steps

  * Setting up the Installation Environment
    * [Setting up the Bungeni Installation Environment](http://code.google.com/p/bungeni-portal/wiki/SettingUpBungeniInstallationEnvironment)
    * [Setting up the Plone Installation Environment](http://code.google.com/p/bungeni-portal/wiki/SettingUpPloneInstallationEnvironment)
  * [Setting up Bungeni](http://code.google.com/p/bungeni-portal/wiki/DeployingBungeniBuildoutWithCapistrano)
  * [Setting up Plone](http://code.google.com/p/bungeni-portal/wiki/InstallingPloneForBungeni#Automated_Install_(using_capistrano))
  * [Setting up Deliverance Portal](http://code.google.com/p/bungeni-portal/wiki/InstallingDeliverancePortal)