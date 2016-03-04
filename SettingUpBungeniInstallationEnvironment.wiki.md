# Introduction

The instructions below are for preparing the pre-requisites for a bungeni installation. Getting the pre-requisites setup correctly ensures a successful buildout. The capistrano recipe has been tested on Ubuntu 8.04.
This is pre-requisite step for DeployingBungeniBuildoutWithCapistrano

# Installing Capistrano

Capistrano is based on [Ruby](http://en.wikipedia.org/wiki/Ruby_programming_language) and is installed via the [RubyGems](http://en.wikipedia.org/wiki/RubyGems) package management system.

The following commands run in a terminal window sets up the basic components for setting up Capistrano, which we will then use to install Bungeni as a capistrano recipe.

```
# first update ubuntu 
sudo apt-get update
sudo apt-get upgrade

# install ruby, ruby gems and other required software 
sudo apt-get install ruby-full build-essential rubygems ssh subversion -y

# the ubuntu 8.04 rubygems installation is usually out of date so 
#we remove the one we just installed and update it with one built from source
sudo apt-get autoremove rubygems -y
wget http://rubyforge.org/frs/download.php/60718/rubygems-1.3.5.tgz
tar xzvf rubygems-1.3.5.tgz
cd rubygems-1.3.5

## note: running without sudo will install the ruby gems within the user's
## home folder e.g. /home/user/.gem/ruby/1.x/bin
## we install it as root and symlink the system install of gem to the one we updated
sudo ruby setup.rb && sudo ln -s /usr/bin/gem1.8 /usr/bin/gem && sudo gem update --system

## now install rake and capistrano
sudo gem install rake -y 
sudo gem install capistrano -y
```

For common errors encountered during Capistrano setup [see here](#Common_problems_while_setting_up_capistrano.md)


# Setup

Create a folder within your home directory, lets call it 'cap-setup'.
In a terminal window change directory to that folder and get the capistrano recipe from svn.

```
cd cap-setup
svn export http://bungeni-portal.googlecode.com/svn/capistrano/bungeni_setup/trunk 
```

It will take a few minutes to download the recipe from svn.
To setup the build we have to edit build configurations. The following :
  * Open 'trunk/config/manual\_configs.rb' in a text editor - and set the parameters appropriately for the 3 lines below :
```
## EDIT THESE ##
set :bungeni_username, "ssh user name"  <-- usually your log in user name
set :password, "ssh password" <-- usually your login password
set :system_build_root, "/home/undesa/disk1/bungeni" <-- path to the folder where you want to install bungeni
```
  * Open 'trunk/config/download\_urls.rb' - and set the paths to the downloadable components as required
```
### python_download_url is the url to python 2.5 source ###
set :python_download_url, "/home/undesa/cache/Python-2.5.4.tgz" # http://www.python.org/ftp/python/2.5.4/Python-2.5.4.tgz
set :python_imaging_download_url, "http://effbot.org/media/downloads/Imaging-1.1.7.tar.gz"

### python24_download_url is the url to python 2.4 source ###
set :python24_download_url, "/home/#{bungeni_username}/cache/Python-2.4.4.tgz" # http://www.python.org/ftp/python/2.5.4/Python-2.5.4.tgz
set :python24_imaging_download_url, "http://effbot.org/media/downloads/Imaging-1.1.7.tar.gz"

### subversion source ###
set :svn_download_url,  "/home/undesa/cache/subversion-1.5.4.tar.gz" #"http://subversion.tigris.org/downloads/subversion-1.5.4.tar.gz"

### varnish source ###
set :varnish_download_url, "/home/undesa/cache/varnish-2.0.6.tar.gz"
```


Now run the following command :

```
cd trunk
cap bungeni_presetup:build_all
```

This will setup the following :
  * Install required OS libraries
  * Build a user python with required binding for :
    * ssl
    * imaging
    * subversion

Also see DeployingBungeniBuildoutWithCapistrano

# Folder Structures

The folder structure used is as follows -- within the bungeni installation folder :
```
.
|-- cap_builds 		<== build folder, not used at runtime
|   |-- imaging  	<== imaging build for python 2.5
|   `-- python
`-- cap_installs 	<== main installation folder
    |-- bungeni_install <== main bungeni installation
    `-- python25 	<== python 2.5

```



# Common problems while setting up capistrano
## Error while updating rubygems

_Sometimes_ _while_ _running_ `sudo gem update --system` _the_ _following_ _error_ _can_ _occur_ :
```
Bulk updating Gem source index for: http://gems.rubyforge.org
Attempting remote update of rubygems-update
ERROR:  While executing gem ... (Gem::GemNotFoundException)
    Could not find rubygems-update (> 0) in any repository
```

_To_ _resolve_ _this_ _we_ _need_ _to_ _clear_ _the_ _source_ _cache_ _of_ _rubygems_ _:_

run `gem env` and not the output :e.g.
```
$gem env

RubyGems Environment:
  - VERSION: 0.9.4 (0.9.4)
  - INSTALLATION DIRECTORY: /var/lib/gems/1.8
  - GEM PATH:
     - /var/lib/gems/1.8
  - REMOTE SOURCES:
     - http://gems.rubyforge.org
```

_clear_ _the_ _source_ _cache_ _file_ _on_ _the_ _GEM_ _installation_ _PATH_ _:_
```
$ sudo rm /var/lib/gems/1.8/source_cache
```
_finally_ _clear_ _the_ _local_ _gem_ _folder_ :
```
rm -rf ~/.gem/
```