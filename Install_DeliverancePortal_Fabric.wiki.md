#summary How to install Deliverance Portal
#sidebar TableOfContents

# Pre-Requisites

You should have completed the following steps :

  * **[How to setup the Pre-requisites](Install_PreRequisites_Fabric.md)**
  * **[How to setup Bungeni Parliamentary Information System](Install_Bungeni_Fabric.md)**
  * **[How to setup Plone Content Management System](Install_Plone_Fabric.md)**

# Build & Setup

## Option 1: Installing using the online package index

Build & deploy using the online package index using the following command :
```
fab -H <host-or-ip> portal_install config_ini:portal
```

See also [Executing Fabric actions](http://code.google.com/p/bungeni-portal/wiki/HowTo_SetupFabricScripts#Executing_fabric_actions)

## Option 2: Installing using the local package index

Use this option only if you have a Bungeni package index running on your local network.
Set the fabric parameters for the cached buildout by setting the `local_cache` parameters appropriately See [Using the local\_cache parameter](http://code.google.com/p/bungeni-portal/wiki/HowTo_ConfigureFabricIni#local_cache)

```
fab -H <host-or-ip>  portal_install config_ini:portal
```

See also [Executing Fabric actions](http://code.google.com/p/bungeni-portal/wiki/HowTo_SetupFabricScripts#Executing_fabric_actions)

## Enable country theme

The deliverance portal is installed with a default theme. To install a country theme, there is a paramater in the Fabric `setup.ini` :

```
[custom]
...
country_theme = default
```

Set this parameter to the appropriate value (_It is recommended to use the ISO 3166-1 alpha-2 codes_) and run :

```
fab -H localhost enable_country_theme config_ini:portal 
```



## Configure the Installation

The deliverance portal gets installed within the Bungeni installation folder in a folder called portal. See [Bungeni Installation folder structure](Install_Final_Folder_Structure.md).

Within the portal folder edit deploy.ini, and set the host-name to the host-name on which you want to serve Bungeni :

```
[DEFAULT]
## change the below host name to whatever public host name you want the server to run on
host_name = put.your.host.name.or.ip.here
```

## Folder Structure

Deliverance gets installed in the `portal` folder within the buildout folder for bungeni.

```
./bungeni
|-- bin
|-- buildconf
|-- cache
|-- data
|-- develop-eggs
|-- eggs
|-- locales
|-- logs
|-- parts
|-- patch
|-- plone
|-- portal
|   |-- bin
|   |-- develop-eggs
|   |-- eggs
|   |-- parts
|   `-- src
|-- src
|-- templates
`-- testdatadmp
```

## Starting and Stopping deliverance

See [using supervisor to start / stop Deliverance](http://code.google.com/p/bungeni-portal/wiki/HowTo_SupervisorServiceManager#deliverance_portal)

# Next Steps

  * **[How to use the Supervisor Service Manager](HowTo_SupervisorServiceManager.md)**