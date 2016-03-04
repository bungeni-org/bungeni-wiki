


# Pre-Requisites

You should have completed the following steps :

  * **[How to setup the Pre-requisites](Install_PreRequisites_Fabric.md)**
  * **[How to setup Bungeni Parliamentary Information System](Install_Bungeni_Fabric.md)**

  1. **The Plone build is an optional deployment configuration which adds a content management system into Bungeni.**
  1. **The Plone build currently works on Ubuntu 10.04 or Ubuntu 12.04**

# Build & Setup

## Option 1: Installing using the online package index

```
fab -H <host-name-or-ip> plone_install config_ini:plone
```

See also [Executing Fabric actions](http://code.google.com/p/bungeni-portal/wiki/HowTo_SetupFabricScripts#Executing_fabric_actions)


## Option 2: Installing using the local package index

Use this option only if you have a Bungeni package index running on your local network.
Set the fabric parameters for the cached buildout by setting the `local_cache` parameters appropriately See [Using the local\_cache parameter](http://code.google.com/p/bungeni-portal/wiki/HowTo_ConfigureFabricIni#local_cache)

```
fab -H <host-name-or-ip> plone_install config_ini:plone 
```

See also [Executing Fabric actions](http://code.google.com/p/bungeni-portal/wiki/HowTo_SetupFabricScripts#Executing_fabric_actions)


## Admin User
Log in as the admin user into plone. The default username and password is "ploneadmin"


## Import demo structure

  * Once the buildout is complete you can import demo data into the plone site.

  * Start the plone site:

```
./fab -H <host-name-or-ip> start_service:plone 
```

  * Import the demo data **(NB:This will overwrite existing content)**:

```
./fab -H <host-name-or-ip> plone_import_site 
```



## Updating a Plone installation

It is standard procedure to re-install a product when any changes have been made to it.
This done in the zmi->portal\_setup. Import the profile of the product that has changed.
Or alternatively use the zmi-portal\_quickinstaller to reinstall said product.


## Folder Structure

Plone gets installed within the Bungeni folder

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
|   |-- Products
|   |-- bin
|   |-- develop-eggs
|   |-- downloads
|   |-- eggs
|   |-- import
|   |-- parts
|   |-- Products
|   |-- src
|   `-- var
|-- src
|-- templates
`-- testdatadmp
```


## Starting and Stopping plone

See [using supervisor to start / stop Plone](http://code.google.com/p/bungeni-portal/wiki/HowTo_SupervisorServiceManager#plone)

# Next Steps

  * **[How to setup Deliverance Portal](Install_DeliverancePortal_Fabric.md)**