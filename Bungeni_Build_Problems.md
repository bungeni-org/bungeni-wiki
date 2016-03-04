# Primary Checklist

  1. Is the package index server running ?
    * Check if your package index server is up and running. This is the web server that hosts the python package index. To find out which is the package server used by the build. Open buildout.cfg and look for the line 'index='. You will see something like :
```
    index = http://pypi.python.org/simple
```
> > Test this link by trying to browse to it. If the link works your package index server is up and running.
  1. SVN does not checkout
    * Do you have an account on the bungeni-portal googlecode project ?
> > > If no then contact the project admins to get an account.
    * I have account on the project but I still cannot checkout using my user name.
> > > Are you sure you are using the google code svn password and not the gmail password ? The googlecode password is available on [http://code.google.com/hosting/settings](http://code.google.com/hosting/settings)
  1. During pre-setup I get file not found for certain components (e.g. subversion)
    * Open the bungeni\_presetup.rb capistrano script -- there are three parameters that can be set there either to a location on the local computer or an http link to the required component. Edit these appropriately to a known location
  1. Svn prompts for missing certicate - but user cannot respond -- this usually happens when there is no ssl root certificate recognised by googlecode installed in the system. Because the prompt is within an automated script the user cannot respond to it. To resolve this issue refer to [How to install root certificate for googlecode](http://code.google.com/p/bungeni-portal/wiki/InstallingRootCertificateForGoogleCode)
    * Unicode Errors like the following when launching bungeni :
```
   ImportError:
/home/undesa/demo_instance/cap_installs/bungeni_install/bungeni/releases/20091207124245/parts/xapian/lib/python/xapian/_xapian.so:
undefined symbol: PyUnicodeUCS4_EncodeUTF8
```
> > > - This usually indicates that the python used by the buildout is out of date / corrupted because of some change in the operating system (or some other reason). The solution in these cases is to backup your db - run the bungeni\_presetup steps again, bootstrap and run a full buildout after deleting the `parts/xapian` folder.
    1. Plone buildout fails while installing psycopg2 with the following error :
```
        NameError: global name 'w' is not defined
```
> > > This occurs on some versions of Ubuntu - to prevent this error install libpq-dev
```
      sudo apt-get install libpq-dev
```
