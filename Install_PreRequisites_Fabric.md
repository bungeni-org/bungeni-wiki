

# Pre-Requisites for Bungeni

The Bungeni Application system has the following pre-requisites :
  * User installed Python either Python 2.6 or Python 2.7;
  * SSL, Imaging, bz2 extensions for user installed Python;
  * System libraries required for building  PostgreSQL from source;
  * OpenOffice.org -- this is used for PDF conversion.

Recommended Linux distribution :
  * Ubuntu 10.04 (32 or 64 bit distro);
  * Ubuntu 12.04 (64 bit distro) - **only runs Python 2.7**.

The build process downloads various components from the internet - so it is **neccessary** for the computer to have access to the internet.

All of these pre-requisites can be automatically installed via a fabric setup script.

So, first we install Fabric - and run the pre-requisite setup scripts which installs all the required pre-requisites.


# Installing Fabric

You will need to first install Fabric.

See [How to Install Fabric](HowTo_InstallFabric.md)

# Setup the Fabric Scripts

After installing Fabric, you will need to configure a few parameters to decide where you want to setup Bungeni.

See [How to setup the Bungeni Fabric Scripts](HowTo_SetupFabricScripts.md) , also see [How to configure the fabric setup.ini file](HowTo_ConfigureFabricIni.md)

# Install the pre-requisites

After setting up the Fabric scripts, you can install the pre-requisites using Fabric.

From the fabric folder run :
```
fab -H <host-name-or-ip> presetup
```

or if you are installing it on `localhost` you can simply run :
```
./fl presetup
```

See also [Running fabric commands](http://code.google.com/p/bungeni-portal/wiki/HowTo_RunFabricCommands#Running_Individual_Commands)

# Folder Structure

At the completion of this step you should have a folder structure in your home directory that looks like this :
```
./bungeni_apps
`-- python26
    |-- bin
    |-- include
    |-- lib
    `-- share

```

# Next Steps

  * **[How to setup Bungeni Parliamentary Information System](Install_Bungeni_Fabric.md)**
  * **[How to setup Plone Content Management System](Install_Plone_Fabric.md)**
  * **[How to setup Deliverance Portal](Install_DeliverancePortal_Fabric.md)**