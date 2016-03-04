

# Introduction

The Fabric setup of bungeni supports different deployment modes configurable via a couple of ini files.

# setup.ini

## The global section

The setup.ini file has a global section which sets certain parameters globally for the build.

```
[global]
system_root = /home/undesa/test
development_build = True
local_cache = False
app_host = 92.243.15.192
distro_override = Ubuntu.10.04-gandi
verbose = true
```


These parameters are explained in the below sections.


### system\_root

This specifies the root folder of the installation

```
[global]
....
system_root = /home/undesa/test
```

Within the root folder the following installation structure is used :
  * bungeni\_apps
    * bungeni - the bungeni installation
      * parts
        * postgresql - postgresql installation
        * db - postgresql data folder
        * xapian - xapian installation
      * plone - the plone installation
      * portal - the deliverance portal installation
    * python26 - python 2.6 or python27 - python 2.7
    * exist - the exist installation
    * rabbitmq - the rabbitmq installation
    * config - folder used for some configuration files
  * cbild - scratch folder used for builds


### development\_build

This indicates whether this is a development build or not.

```
[global]
....
development_build = True
```

When set to `False` - fabric does anonymous checkouts from subversion.
When set to `True` - fabric checks out source based on the user and password set in the `[scm]` section.
See also [Configuring a Fabric build](http://code.google.com/p/bungeni-portal/wiki/HowTo_SetupFabricScripts#Configuring)


### local\_cache

Indicates whether the build must make use of a local package index and archive cache.

```
[global]
....
local_cache = False
```

For most deployments you would set this to false.
If you have a package index for bungeni in your network, you can set this parameter to true - but then you will need to set **all** the following parameters :
  * local\_index in `[bungeni]`, `[portal]` and `[plone]`
  * local\_url in `[postgresql]`, `[xapian]` and `[xapian-bindings]`

### app\_host

Specifies the IP address or domain name of the deliverance portal host on which you are deploying.

```
[global]
....
app_host = 92.243.15.192
```

If you are deploying only onto the local computer and not accessing the installation from another computer, you can set this to `localhost`.
If you are deploying it in a LAN for test purposes you can set it to the IP address of the computer on which you have installed it.
If you are deploying bungeni on the internet hosted on a domain -- you can set this to `localhost` to proxy it behind a domain name. See [Portal Deployment Configuration](#portal_deployment_configuration.md)


### verbose

Setting `verbose=true` runs the build in verbose mode i.e. with a much richer set of output messages (useful for debugging)


### distro\_override

Specify a distro package list: Recommended linux distributions are 10.04 or 12.04


```
[global]
....
distro_override = Ubuntu:10.04-gandi
```

Fabric queries a file called `distro.ini` for a list of required packages. Required packages are specified in sections grouped by distribution name and release id e.g. :
```
....
[Ubuntu]
12.04 = 
	package-dev
	linux-headers
....
```

The package name and the distribution are automatically detected by fabric. To override this behavior you can specify a distribution name and release id specific to your requirements. The distribution name and package name must be separated by a colon (':').

In the example above `distro_override` has been specified as `Ubuntu:10.04-gandi`. Fabric will now look for the required packages list in `distro.ini` under the following section :
```
[Ubuntu]
10.04-gandi = 
	pacakge-spec
	package-two
```

**NB: Specifying the distro as 12.04 requires you to set the python version for the supervisor, bungeni, plone and portal sections to 2.7
See [HowTo\_ConfigureFabricIni#The\_bungeni,\_plone\_and\_portal\_sections](HowTo_ConfigureFabricIni#The_bungeni,_plone_and_portal_sections.md)**

### release

This specifies the name of the release to use. The available releases are listed in `release.ini`, only an existing release can be specified here.

e.g.

setup.ini :
```
[global]
....
....
release = 2011-12-26
```

corresponding listing of the release in `release.ini` :
```
[2011-12-26]
bungeni=8327
plone=8327
portal=8123
```


## The supervisor section

```
[supervisor]
host=localhost
port=8888
python=2.6
```

These parameters specify the public host and port name on which the supervisor http server starts up.

For most situations `host` can be left as `0.0.0.0` which indicates all public interfaces

```
[supervisor]
.....
host = 0.0.0.0
```

The `port` parameter provides the access port for the http monitor interface :

```
[supervisor]
......
supervisor_port = 8888
```

setting the above makes the supervisor interface accessible on http://localhost:8888.
Note that setting the supervisor host to `0.0.0.0` makes it listen on all network interfaces. This may not always be desirable, in such instances set it to a specific IP.

The last parameter `python` specifies the python installation where the supervisor daemon will install into.

## The scm section

This section has 2 parameters - the first is the svn user name , and the second is the password :

```
[scm]
user=my.svn.user.name
pass=azo28nd8
```

**Note that the user and pass parameters are used only when the `development_build` parameter is set to `True` in `[global]*`**


## The bungeni, plone and portal sections

bungeni, plone and portal have similar configuration parameters.

```
[bungeni]
local_index=http://192.168.0.14:6543/bungeni
python=2.6
repo=bungeni-portal.googlecode.com/svn/bungeni.buildout/trunk
dump_file=dmp_undesa.txt
http_port = 8081
admin_user=admin
admin_password=password
attachments_folder=fs
attachments_archive=fs.tar.gz
```

  * `local_index` is the address of the local package index. This is used only when `local_cache` is set to `True` in `[global]`.
  * `python` is the version of python to use for bungeni (2.6 or 2.7). Specifying a python version here will cause a custom built python of that version to be built during the `presetup` action. e.g. if you specify 2.6 for `python` in the `[bungeni]` section, and 2.7 for `python` in the `[portal]` section both python 2.6 and 2.7 will be built by fabric during `presetup`.
  * `repo` is the address of the bungeni subversion repository minus the protocol part of the URL. Fabric sets it appropriately depending upon whether you use `development_mode` or not.
  * `dump_file` the name of the database dump file. this is expected to be in the `testdatadmp` folder
  * `attachments_folder` and `attachments_archive`  the name of the folder where attachments are stored. the archive contains attachment documents part of test data.
  * `http_port` is the port on which the http service for the application listens on.


### portal deployment configuration

The **portal** section provides certain extra deployment parameters, which are explained below.

The deliverance portal is an application proxy -- and can be proxied behind a virtualhost / domain. The following parameters allow us to do this easily

The `theme` parameter specifies the theme layout file to be used by the deliverance portal. The path is always relative to `bungeni_installation/portal/src/portal.theme/portal/theme/static/themes`.

```
[portal]
.......
##themes are relative to the portal address /static/themes/ uri
theme = layout.html
```

The `http_port` parameter specifies the port on which the deliverance proxy runs :
```
[portal]
.......
http_port=8080
```

The `static_port` parameter specifies the port on which static content is served :
```
static_port=8083
```

The `web_server_host` and `web_server_port` parameters have been explained below in the inline comment :
```
## Next two parameters are for deployment purposes under
## web server (e.g. Apache, Nginx ...). 
## Running in a development environment --- 
## if in a development environment and not deploying behind 
## a web server use app_host value for web_server_host ; and
## use http_port value for web_server_port
## 
## Runing in a deployment environment ---
## if you are deploying it behind a web server and a domain 
## set web_server_host to the domain e.g. kenya.bungeni.org
## set web_server_port to 80 -- which means you can now 
## browse bungeni on http://kenya.bungeni.org/
web_server_host = test.bungeni.org
web_server_port = 80
```

For example nginx and apache configurations see [How to proxy Bungeni behind a web server](HowTo_ProxyBungeniBehindWebServer.md)


## The python24, python25, python26, python27, imaging sections

These sections take a single parameter :

```
[python25]
download_url = http://www.python.org/ftp/python/2.5.5/Python-2.5.5.tgz
```

```
[python27]
download_url = http://www.python.org/ftp/python/2.6.7/Python-2.6.7.tgz
```

`download_url` can either be a http / ftp URL or a file system path (/home/files/file.tar.gz) to the python source archive.


## The postgresql, xapian, and xapian-bindings sections

These sections take a single parameter `local_url`. This parameter is used only when `local_cache` is set to `True`.


# release.ini

This file lists available releases, the name of a section identifies the name of the release.
```
[2011-12-26]
bungeni=8327
plone=8327
portal=8123


[2011-11-13_release_kenya]
bungeni=8127
plone=8117
portal=8123
```

the above example lists 2 releases identified in `release.ini`.

Every release section must list 3 properties : bungeni, plone and portal.

Each of these properties has a value corresponding to a revision number on svn.






# distro.ini

## The distribution section

The primary section is the name of the distribution and within that the release numbers are described as options.

```
[Ubuntu]
8.04 =
	wget
	zip
	unzip
	.....
10.04 = 
	build-essential 
	libjpeg62-dev 
	libfreetype6-dev 
	.....
```

The Distribution name and release number is detected automatically using `lsb_release`. In some distros `lsb_release` may not be available, in such cases create your own section within `distro.ini` and use the `distro_override` flag in `[global]` (described above) to specify the package list of your distribution.

The `distro.ini` also allows comments within package lists

e.g. this is OK :
```
[Ubuntu]
8.04 =
	wget
	zip
	unzip
	# for building from source
	build-essential 
	# for python
	libjpeg62-dev 
```

e.g. this is NOT OK :
```
[Ubuntu]
8.04 =
	wget
	zip
	unzip # for building from source <== NOT OK
	build-essential 
# for python <== NOT OK
	libjpeg62-dev 
```