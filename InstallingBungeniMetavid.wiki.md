This page contains instructions for installing Bungeni Metavid on Linux. It may be possible to install Bungeni Metavid on other platforms but this has not been tested.

# Introduction

Bungeni Metavid is a PHP application for the transcription of parliamentary records and is built on [Metavid](http://metavid.ucsc.edu/)

# Requirements

Metavid Bungeni depends on

  * MediaWiki (current stable version is 1.12.0)
  * Web Server such as Apache or IIS
  * PHP version 5.0 or later (5.1.x recommended)
  * MySQL 4.0 or later
  * mod\_annodex or oggz-chop
  * Various mediawiki Extensions :
    * SemanticMediawiki
    * Parser Functions

# Preparing the installation

  * Installing apache2, mysql and php and php-mysql:
```
sudo apt-get install apache2 mysql-server php5 php5-mysql
sudo apache2ctl restart # restart apache to load php and other modules
```

  * Install mod\_annodex, this will install the following:
    * libannodex0 - annotated and indexed media library
    * libcmml1 - Continuous Media Markup Language document handler
    * libogg0 - Ogg Bitstream Library
    * libsndfile1 - Library for reading/writing audio files
    * liboggz1 - convenience interface for Ogg stream I/O
```
sudo apt-get install libannodex0 annodex-tools cmml-tools
```

  * Installing libgd image manipulation libraries :
```
sudo apt-get install libgd2-xpm libgd2-xpm-dev php5-gd
```

  * Configuring PHP  - edit php.ini
```
sudo vim /etc/apache2/php/php.ini
```
    * Change - Maximum Upload Filesize. Video files are quite large so upload\_max\_filesize needs to be changed accordingly
```
 #from : upload_max_filesize = 2M
 #to :
 upload_max_filesize = 2000M
```
    * Change - Memory Limit
```
#from : memory_limit = 8M
# to
memory_limit = 16M
```

  * Configuring MySQL - we will need to add a mysql user for mediawiki :
```
#run the mysql admin from the command line 
mysql -u root -p mysql
```
    * when prompted, enter the root password for mysql
    * add a user called `wikiuser` :
```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'wikiuser'@'localhost'
    IDENTIFIED BY 'some_pass' WITH GRANT OPTION;
```

# Installing MediaWiki

  * We will install mediawiki in /var/www which is the document root folder for apache on ubuntu :
```
cd /var/www
#get mediawiki from the wikimedia svn repo
svn checkout http://svn.wikimedia.org/svnroot/mediawiki/trunk/phase3 mvwiki
#make the mediawiki config subdirectory writable by the web server.
cd /var/www/mvwiki
sudo chmod a+w config
```

  * Launch a web browser and run :
```
http://ip-of-server/mvwiki
```
    * Click setup, and on the next page enter the required settings
    * Enter the user name and password for mysql that you created in the previous step
    * Click 'install mediawiki'
    * Finally remove the installation configuration files :
```
undesa@mediawiki:/var/www/mvwiki$ sudo mv config/LocalSettings.php .
undesa@mediawiki:/var/www/mvwiki$ sudo rm -rf config
```
    * mediawiki should be up and running on, http://server-ip-address/mvwiki

## Required Extensions for Metavid

Metavid depends on various mediawiki extensions, the installation procedures for these extensions is listed below in the order of installation

### Installing Parser Functions
Download the extension into the extensions folder of Mediawiki

```
cd extensions
svn co http://svn.wikimedia.org/svnroot/mediawiki/trunk/extensions/ParserFunctions
```

Add the following line to LocalSettings.php

` require_once("$IP/extensions/ParserFunctions/ParserFunctions.php"); `

More information about installing ParserFunctions extension may be found [here](http://www.mediawiki.org/wiki/Extension:ParserFunctions)

### Installing Semantic Mediawiki

  * To install semantic mediawiki,do the following :
```
cd /var/www/mvwiki/extensions
svn checkout http://svn.wikimedia.org/svnroot/mediawiki/trunk/extensions/SemanticMediaWiki SemanticMediaWiki

```

  * Add the following to LocalSettings.php in the mediawiki installation folder at /var/www/mvwiki/ :
```
include_once('extensions/SemanticMediaWiki/includes/SMW_Settings.php');
enableSemantics('localhost');
```

  * Log in to mediawiki using the WikiSysop user (the admin user), and access the page, '/wiki/Special:SMWAdmin' in your browser. Follow and complete the setup instructions for semantic mediawiki.


### Installing Other Extensions

put stuff here...

## Installing Metavid

  * Get metavid from the svn repository :
```
cd /var/www/mvwiki/extensions
svn co http://bungeni-portal.googlecode.com/svn/metavid.bungeni/trunk MetavidWiki
```
  * Edit LocalSettings.php in the mediawiki installation folder to add the following before and after semantic mediawiki extension settings :
```
##next line added for metavidwiki extension
require_once( "$IP/extensions/ParserFunctions/ParserFunctions.php" );

##next 3 lines added for metavidwiki extension
$smwgNamespaceIndex=100;
$mvNamespaceIndex=$smwgNamespaceIndex + 6;
include_once("$IP/extensions/MetavidWiki/includes/MV_Settings.php");

##next 2 lines are from the semantic mediawiki installation from the previous step
include_once("$IP/extensions/SemanticMediaWiki/includes/SMW_Settings.php");
enableSemantics('localhost');

##next line added for enabling metavid
enableMetavid();
```

  * Install the tables required by metavid wiki into the mediawiki database :
```
cd /var/www/mvwiki/extensions/MetavidWiki/maintenance
mysql -u wikiuser -p wikidb < mv_tables.sql
```