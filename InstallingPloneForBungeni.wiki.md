

# Manual Install

## Pre-requisites

  * Build environment (on ubunutu do a `sudo apt-get install build-essentials` )
  * user Python 2.4 built from source. (on ubuntu /debian you will need to install development libaries/headers for imaging (libjpeg62-dev, libfreetype6-dev, libpng12-dev)  and bz2 (libbz2-dev) libraries) and for ssl support openssl and libssl (openssl, libssl-dev)


## SSL support for Python

In ubuntu installing openssl and libssl-dev does not result in Python getting built with ssl support as the default ssl paths for include/ lib referenced by the Python build are different in Ubuntu. To build ssl support into Python on Ubuntu set the CPPFLAGS and LDFLAGS environment variables as follows :

```
 CPPFLAGS=-I/usr/include/openssl LDFLAGS=-L/usr/lib/ssl
```

now run ./configure ; make ; make install

To test if python has ssl support run this in the python shell :

```
import urllib
f = urllib.urlopen('https://svn.openplans.org')
# if it returns no errors ... the ssl support works.. 
```

(tested on Ubuntu 8.04)


## Setup (manual steps)

Note:
the python setuptools package (a python egg) is used by buildout, if you have an older version of setuptools, you might need to update it manually first (from http://cheeseshop.python.org/pypi/setuptools/) in the python used for zope.
Update instructions at: http://peak.telecommunity.com/DevCenter/EasyInstall#installation-instructions

If you dont have setuptools installed, buildout downloads and installs it automatically.

### Core Installation

In order to install it, use buildout. Plone for Bungeni is always installed within the Bungeni Buildout folder and resides in a sub-folder called plone:

```
$ cd bungeni_buildout_folder
$ svn co https://bungeni-portal.googlecode.com/svn/plone.buildout/trunk/ --username <username> plone
$ cd plone
$ python bootstrap.py # not the system python! The one you use with Plone, i.e. 2.4.x. 
$ ./bin/buildout  # wait a while..
```

Once this has successfully completed. Add the 1st user to plone - the admin user :
```
./bin/addzope2user admin password
```

Now edit the zope instance home variable to an absolute path pointing to the plone buildout folder :

```
## was %define INSTANCE .
%define INSTANCE /home/undesa/bungeni_portal/current/plone
```

Create a folder called var/filestorage :
```
mkdir -p ./var/filestorage
```

Now you can start plone :

```
./bin/paster serve etc/deploy.ini
```

### Installing specific products used by Plone for Bungeni

Log in as the admin user into plone.

In the Zope ZMI create a new plone site called "site"

Select the following in the installation profiles for the plone site:
  * Bungeni CMS Policy
  * Bungeni CMS Theme

Now log in to the newly created plone site as admin.

Click on site-setup -> Add on products.

  * Bungeni CMS Policy & Bungeni CMS theme, and click install.
  * select 'GroupWorkspace Profile' in the same page and click 'install'.
  * do the same for "BungeniHelpCenter", ContentWellPortlets", plonegalleryview, iqpp.plone.commenting and FCKeditor.
  * Adjust the commenting options: These are found on the Plone Control Panel under Add-on Product Configuration, the button named 'Commenting'.
    * Enable the following settings: Comments, Moderation and Show Preview.

### Import demo structure

  * On the filesystem create an import folder in the root plone directory (the buildout foldr for plone) .The folder should be called import.

  * Copy and unzip the contents of [http://dist.bungeni.org/plone/import/import-0.1.tar.gz](http://dist.bungeni.org/plone/import/import-0.1.tar.gz) into that folder

  * Run the python script 'import\_data' in the /etc folder (using the plone python). Edit the file appropriately and put in the necessary values for username, password and the plone site that you want to import to.

## Updating existing Plone installation

It is standard procedure to re-install a product when any changes have been made to it.
This done in the zmi->portal\_setup. Import the profile of the product that has changed.
Or alternativley use the zmi-portal\_quickinstaller to reinstall said product.

# Automated Install (using capistrano)

The assumption here is that you have already installed bungeni using capistrano.

## Installing pre-requisites

Edit plone\_presetup.rb , specifically the following 2 settings as required :
```
set :python24_download_url, "/home/undesa/cache/Python-2.4.4.tgz" # http://www.python.org/ftp/python/2.5.4/Python-2.5.4.tgz
set :python24_imaging_download_url, "http://effbot.org/media/downloads/Imaging-1.1.7.tar.gz"
```

Now run the following command :
```
cap plone_presetup:build_all
```

## Installing Plone

The following command will install plone :

```
cap plone_install:setup_from_cache plone_install:full_from_cache
```

If you are using the public index instead of the cached index, do this :

```
cap plone_install:setup plone_install:full
```



After this you will have to run the following steps manually [Installing specific products used by Plone for Bungeni](#Installing_specific_products_used_by_Plone_for_Bungeni.md)




TODO