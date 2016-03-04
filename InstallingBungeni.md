**Note** : An easier way to setup bungeni on Ubuntu 8.04 is to use a capistrano recipe. For details click here SettingUpBungeniInstallationEnvironment and DeployingBungeniBuildoutWithCapistrano. This page documents the manual steps required to install Bungeni

## 1. Pre-requisites

  * Build environment (on ubunutu do a `sudo apt-get install build-essentials` )
  * user Python 2.5 built from source. (on ubuntu /debian you will need to install development libaries/headers for imaging (libjpeg62-dev, libfreetype6-dev, libpng12-dev)  and bz2 (libbz2-dev) libraries) and for ssl support openssl and libssl (openssl, libssl-dev)
    * For comipling the xapian bindings you will have to switch the user python to the primary python on the system either by aliasing it (alias='/my/path/to/myuser/python') or by changing the python referenced by /usr/bin/python (on debian /ubuntu you can switch between system pythons by using the update-alternatives command). [UPDATE: setting the environment variable PYTHON to the path of the user python binary also appears to work : `export PYTHON=/home/user/bungeni/python/bin/python'`
  * PostGreSQL build from source requires development libraries/headers for: bison, flex ,readline (libreadline5-dev), zlib (zlib1g-dev). (on ubuntu installed via apt-get)

## 2. SSL support for Python

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

## 3. Python bindings for SVN

For installing on Ubuntu 8.04 - a custom build of the python subversion bindings may be required (especially if you get an error while starting bungeni which looks like : 'libtool missing file libneon.la' ) . Building the subversion bindings requires building subversion from source.  To do a custom build of subversion the following prerequisites are required :
  * Custom build of libneon - this is required because subversion 1.5.4 requires libneon 0.28.3 while Ubuntu 8.04 ships with libneon 0.27.2.
    * Get the appropriate version of libneon source from [http://svn.webdav.org/repos/projects/neon/tags/](http://svn.webdav.org/repos/projects/neon/tags/) (e.g. 0.28.3 is the correct version for subversion 1.5.4)
    * To build libneon you will need to setup autoconf and automake. To do that install the following packages :
```
sudo apt-get install libtool automake autoconf
```
    * Now switch to the folder where you downloaded the libneon source and run `./autogen.sh` to generate the make files
    * Build libneon with :
```
 ./configure --prefix=/path/to/install/neon --with-ssl=openssl 
make
make install
```
  * Prerequisities for building subversion :
```
sudo apt-get install libaprutil1-dev swig
```
  * Building subversion :
    * Switch to the subversion source folder
    * First we need to tell subversion to use our custom build of neon :
```
export neon_config=/path/to/neon/installation/bin/neon-config
```
    * Now build subversion :
```
./configure --prefix=/home/my/custom/svn/install PYTHON=/home/my/user/python/install/path
make
make install
```
    * Now build the python subversion bindings. from the subversion source build root folder run the following :
```
make swig-py
# check the installation
make check-swig-py
# install 
make install-swig-py
```
    * svn-python gets installed under the custom subversion installations lib/svn-python folder, so for our custom python to use the subversion module, create a file called 'subversion.pth' with the path to the lib/svn-python and place it inside the 'site-packages' folder of the custom python build.
    * This completes the setup of the python subversion bindings


## Setup

Note:
the python setuptools package (a python egg) is used by buildout, if you have an older version of setuptools, you might need to update it manually first (from http://cheeseshop.python.org/pypi/setuptools/) in the python used for zope.
Update instructions at: http://peak.telecommunity.com/DevCenter/EasyInstall#installation-instructions

If you dont have setuptools installed, buildout downloads and installs it automatically.

In order to install it, use buildout. These steps should be all you need to setup a Zope instance with all the required products and libraries:

```
$ svn co https://bungeni-portal.googlecode.com/svn/bungeni.buildout/trunk/ --username <username>
bungeni
$ cd bungeni
$ python bootstrap.py # not the system python! The one you use with Zope, i.e. 2.5.x. 
$ ./bin/buildout  # wait a while..
$ ./bin/setup-database # this will setup the postgres db
$ ./bin/paster serve debug.ini # this will launch the server in debug mode...
```

On a Mac, you may need to increase shmmax value to get postgresql up and running.

The buildout includes DeadlockDebugger, so if you start Zope in debug mode, it will stop at a debugger prompt if an exception is raised.


## Ubuntu Intrepid (8.10) Prerequistes
```
sudo apt-get install build-essential python-dev zlib1g-dev libreadline5-dev uuid-dev python-dev python-subversion
```


Also see ConfiguringBungeni.
