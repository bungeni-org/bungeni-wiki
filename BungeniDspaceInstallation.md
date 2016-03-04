## Setup PostGresQL

  * Add a database called 'dspace' to Postgres
  * Create a login role called 'dspace' with password 'dspace'
  * Make the 'dspace' role the owner of the 'dspace' db.

## Dspace installation procedure

  * Install the JDK on your system : `sudo apt-get install sun-java6-jdk`
  * Download Apache Maven : `wget http://www.apache.org/dist/maven/binaries/apache-maven-2.0.9-bin.tar.gz`
  * Download Apache Ant : `wget http://www.apache.org/dist/ant/binaries/apache-ant-1.7.1-bin.tar.gz`
  * Downloa [DSpace](http://sourceforge.net/project/dspace) : `wget http://voxel.dl.sourceforge.net/sourceforge/dspace/dspace-1.5.0-release.tar.gz`
  * Set the JAVA\_HOME to the jdk directory : `export JAVA_HOME=/usr/lib/jvm/java-5-sun`
  * Extract and install maven to ~/apps/maven
  * Extract and install ant to ~/apps/ant
  * Extract DSpace to ~/builds/dspace
  * Edit dspace.cfg :
```
	# Edit ~/builds/dspace/dspace/config/dspace.cfg, in particular you'll need to set these properties:
	dspace.dir -- must be set to the [dspace] (installation) directory.
	dspace.url -- complete URL of this server's DSpace home page.
	dspace.hostname -- fully-qualified domain name of web server.
	dspace.name -- "Proper" name of your server, e.g. "My Digital Library".
	db.password -- the database password you entered in the previous step.
	mail.server -- fully-qualified domain name of your outgoing mail server.
	mail.from.address -- the "From:" address to put on email sent by DSpace.
	feedback.recipient -- mailbox for feedback mail.
	mail.admin -- mailbox for DSpace site administrator.
	alert.recipient -- mailbox for server errors/alerts (not essential but very useful!)
	registration.notify -- mailbox for emails when new users register (optional) 
```
  * Create the directory for the DSpace installation (i.e. ~/apps/dspacesrv ) : `mkdir ~/apps/dspacesrv`
  * Now run maven : `cd ~/builds/dspace/dspace; ~/apps/maven/bin/mvn package`
    * this will take a while to complete
  * Now run ant after maven has completed: `cd ~/builds/dspace/dspace/target/dspace-1.5.0.dir/ ; ~/apps/ant/bin/ant fresh_install`
  * Once ant runs successfully dspace has been installed under ~/apps/dspacesrv


## Configuring Tomcat

Tell your Tomcat installation where to find your DSpace web application(s). As an example, in the 

&lt;Host&gt;

 section of your [tomcat](tomcat.md)/conf/server.xml you could add lines similar to the following:
```
<!-- DEFINE A CONTEXT PATH FOR DSpace JSP User Interface  -->
<Context path="/jspui" docBase="/home/undesa/apps/dspacesrv/webapps/jspui" debug="0" reloadable="true" cachingAllowed="false" allowLinking="true"/>
	
<!-- DEFINE A CONTEXT PATH FOR DSpace OAI User Interface  -->
<Context path="/oai" docBase="/home/undesa/apps/dspacesrv/webapps/oai" debug="0" reloadable="true" cachingAllowed="false" allowLinking="true"/>

<!-- DEFINE A CONTEXT PATH FOR DSpace XMLUI User Interface  -->
<Context path="/xmlui" docBase="/home/undesa/apps/dspacesrv/webapps/xmlui" debug="0" reloadable="true" cachingAllowed="false" allowLinking="true"/>
```

## Configuring the Bungeni Theme

  * Manakin is installed by default with DSpace 1.5 within the dspace/webapps/xmlui folder.
  * Edit the dspace.cfg file in dspace to include the following xml ui configurations :
```
 #
  # Repository metadata
  #
  xmlui.repository.identifier = DSpace Manakin
  xmlui.repository.description = This is the default repository description
  xmlui.repository.publisher = Default DSpace publisher
  xmlui.repository.subject = Default repository subject
  xmlui.repository.title = DSpace XMLUI :: Manakin
```
  * Get the bungeni dspace theme, the theme is installed into the dspace/webapps/xmlui/themes folder :
```
svn co http://bungeni-portal.googlecode.com/svn/dspace.bungeni/trunk/xmlui/themes/bungeni ~/apps/dspacesrv/webapps/xmlui/themes/bungeni
```
  * Add the theme as the default theme to the Dspace installation :
    * Open Manakin's configuration file, [dspace](dspace.md)/config/xmlui.xconf, scroll to the bottom and locate the 

&lt;themes&gt;

 element.
    * Add a new tag 

&lt;theme&gt;

 element inside the 

&lt;themes&gt;

 element as below, to make the below theme the default theme, make sure that it is the first 

&lt;theme&gt;

 element inside 

&lt;themes&gt;

:
```
<theme name=”bungeni” regex=".*" path=”bungeni/”/>
```

  * Restart Tomcat. The themed view for Dspace should be accessible on http://

&lt;address:port&gt;

/xmlui

## Installing the dspace-koha cross search application

The dspace-koha cross search application is a dspace extension application that makes use of marc4j to index Koha marc records.

To install the application :
```
 wget http://bungeni-portal.googlecode.com/svn/dspace.bungeni/trunk/cross-search/digitalrepository.war ~/apps/tomcat/webapps
```

Restart tomcat, and the cross search application should be visible from http://server:port/digitalrepository


## Setting up the cross search and indexing of Koha records
<<<<<<<<to be done>>>>>>

Retrieving the cross search interface from svn

The web archive(.war) file and other files are available from the svn repository situated at
http://bungeni-portal.googlecode.com/svn/dspace.bungeni/trunk

A readme file is included in the web archive giving details of how to make the cross search app communicate with your dspace installation.

The readme file also contains information on how to configure the cross search application using the crsssearch.cfg file