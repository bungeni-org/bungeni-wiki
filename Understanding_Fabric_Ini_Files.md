

# Introduction

The Fabric setup of bungeni supports different deployment modes configurable via a couple of ini files.

# bungeni.ini

## The global section

The bungeni.ini file has a global section which sets certain parameters globally for the build.

```
[global]
system_root = /home/undesa/test
development_build = True
local_cache = False
app_host = 92.243.15.192
distro_override = Ubuntu.10.04-gandi
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
    * python25 - python 2.5
    * python24 - python 2.4
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

Specifies the IP address or domain name of the host on which you are deploying.

```
[global]
....
app_host = 92.243.15.192
```

If you are deploying only onto the local computer and not accessing the installation from another computer, you can set this to `localhost`.

### distro\_override

Specify a distro package list.

```
[global]
....
distro_override = Ubuntu:10.04-gandi
```

Fabric queries a file called `distro.ini` for a list of required packages. Required packages are specified in sections grouped by distribution name and release id e.g. :
```
....
[Ubuntu]
8.04 = 
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

## The scm section

This section has 2 parameters - the first is the svn user name , and the second is the password :

```
[scm]
user=ashok.hariharan
pass=azo28nd8
```

Note that the user and pass parameters are used only when the `development_build` parameter is set to `True` in `[global]`

## The python24, python25, imaging sections

These sections take a single parameter :

```
[python25]
download_url = http://www.python.org/ftp/python/2.5.5/Python-2.5.5.tgz
```

`download_url` can either be a http / ftp URL or a file system path (/home/files/file.tar.gz) to the python source archive.

## The bungeni, plone and portal sections

```
[bungeni]
local_index=http://192.168.0.14:6543/bungeni
repo=bungeni-portal.googlecode.com/svn/bungeni.buildout/trunk
dump_file=dmp_undesa.txt
```

`local_index` is the address of the local package index. This is used only when `local_cache` is set to `True` in `[global]`.
`repo` is the address of the bungeni subversion repository minus the protocol part of the URL. Fabric sets it appropriately depending upon whether you use `development_mode` or not.

## The postgresql, xapian, xapian-bindings and exist sections

These sections take a single parameter `local_url`. This parameter is used only when `local_cache` is set to `True`.


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
