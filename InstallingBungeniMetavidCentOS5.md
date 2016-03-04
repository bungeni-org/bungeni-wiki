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

# Installation

Install PHP, MySQL, Apache and the PHP image manipulation libraries.
```
su -c 'yum -y install php mysql apache php-mysql php-gd php-xml ImageMagick'
```

Additional instructions for installing the LAMP stack and dependencies for mediawiki on RPM based distros based on Red Hat may be found [here](http://www.mediawiki.org/wiki/Manual:Running_MediaWiki_on_Red_Hat_Linux)
**Configuring PHP  - edit php.ini
```
sudo vim /etc/php.ini
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
memory_limit = 20M
```**

## Mod\_Annodex Installation

Mod\_annodex depends on
  * libannodex, which in turn depends on
  * libcmml,
  * liboggz and
  * libogg

```
rpm -i ftp://bo.mirror.garr.it/pub/1/fedora/linux/extras/6/x86_64/libcmml-0.9.1-3.fc6.x86_64.rpm

rpm -i ftp://ftp.pbone.net/mirror/www-ccrma.stanford.edu/planetccrma/mirror/centos/linux/planetccrma/5/x86_64/liboggz-0.9.4-3.el5.ccrma.x86_64.rpm

rpm -i ftp://ftp.muug.mb.ca/mirror/fedora/linux/extras/6/x86_64/libsndfile-1.0.17-2.fc6.x86_64.rpm

rpm -i ftp://ftp.muug.mb.ca/mirror/fedora/linux/extras/6/x86_64/libannodex-0.7.3-7.fc6.x86_64.rpm

rpm -i ftp://ftp.muug.mb.ca/mirror/fedora/linux/extras/5/x86_64/mod_annodex-0.2.2-4.fc5.x86_64.rpm
```

## Mediawiki Installation

  * Add a mysql user for mediawiki :
```
#run the mysql admin from the command line 
mysql -u root -p mysql
```
    * when prompted, enter the root password for mysql
    * add a user called `wikiuser` :
```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'wikiuser'@'localhost'
    IDENTIFIED BY 'some_pass' WITH GRANT OPTION;
mysql>quit;
```

  * Download Mediawiki from SVN
```
cd /var/www
#get mediawiki from the wikimedia svn repo
svn checkout http://svn.wikimedia.org/svnroot/mediawiki/trunk/phase3 wiki
#make the mediawiki config subdirectory writable by the web server.
cd /var/www/wiki
sudo chmod a+w config
```
  * Change apache www root to wiki directory by editing
```
vi /usr/local/apache2/conf/httpd.conf

 #DocumentRoot "/usr/local/apache2/htdocs"
 DocumentRoot "/var/www/wiki"
```
  * Launch a web browser and run :
```
http://server-ip-address
```
    * Click setup, and on the next page enter the required settings
    * Enter the user name and password for mysql that you created in the previous step
    * Click 'install mediawiki'
    * Finally remove the installation configuration files :
```
undesa@mediawiki:/var/www/wiki$ sudo mv config/LocalSettings.php .
undesa@mediawiki:/var/www/wiki$ sudo rm -rf config
```
    * mediawiki should be up and running on, http://server-ip-address

Note: If you get a blank page after successfully installing Mediawiki, ensure the DocumentRoot for apache is set to the wiki directory and that php-xml is properly installed.

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