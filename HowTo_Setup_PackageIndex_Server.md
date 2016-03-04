# Introduction
This page describes how to setup a local index to serve up packages that are needed for a Bungeni installation.

## 1. Installing repoze.bfg

> It is advisable to install repoze.bfg into a virtualenv

  * UNIX
    * Install python headers and build-essential
```
sudo apt-get install python26-dev build-essential
```
    * Ensure that setuptools is installed - download [ez\_setup.py](http://peak.telecommunity.com/dist/ez_setup.py); invoke it using the Python interpreter you want to install setuptools into:
```
$ sudo python ez_setup.py
```
    * Install the virtualenv package - use the easy\_install command:
```
   $ easy_install virtualenv
```
    * Create the virtual python environment
```
      $ virtualenv --no-site-packages pkgindex
```
    * Install repoze.bfg Into the virtual python environment
```
      $ bin/easy_install -i http://dist.repoze.org/bfg/current/simple repoze.bfg
```

Note:Your folder structure should now look like this:
```
    ~/pkgindex/bin
    ~/pkgindex/include
    ~/pkgindex/lib
```

# Setting up repoze.pkgindex

Install this package in the root of the virtualenv:
```
$ svn co https://bungeni-portal.googlecode.com/svn/repoze.pkgindex/trunk/  --username <username>
```

Note:Your folder structure should now have one more folder and 6 extra files as follows:
```
    ~/pkgindex/repoze
    ~/pkgindex/demo.ini
    ~/pkgindex/deployment.ini
    ~/pkgindex/ez_setup.py
    ~/pkgindex/README.txt
    ~/pkgindex/setup.cfg
    ~/pkgindex/setup.py
```

Create the index folder in the root of the virtualenv folder.
Name it 'index' - Add any packages and sub-folders necessary for your installation into this folder.

Using the interpreter from the virtualenv created when installing repoze.bfg, invoke the following command when inside the project directory against the setup.py:
```
$ ./bin/python setup.py develop
```
Run the package from the virtualenv root folder:
```
$ ./bin/paster serve deployment.ini
```

You can now access your package index at localhost:6543 or 127.0.0.1:6543

Note:
You can modify the deployment.ini file to look for the index in a different folder other than the one named 'index' and to run on a different port other than the default '6543'.