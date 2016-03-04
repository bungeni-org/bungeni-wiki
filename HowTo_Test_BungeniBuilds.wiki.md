# Introduction

  * Hudson - automated build monitor for Bungeni; runs continuous integration builds and reports back errors
  * Selenium RC - automated test server proxy - runs prepared unit tests on Bungeni using a headless browser


# Pre-requisities

  * Install Sun Java6 -
```
	sudo apt-get install sun-java6-jdk
```

# Setting up Hudson

  * Install Tomcat
  * Get the tomcat binary and extract to folder :
```
	wget http://apache.imghat.com/tomcat/tomcat-5/v5.5.28/bin/apache-tomcat-5.5.28.tar.gz
	mkdir tomcat-server	
	tomcat -C tomcat-server -xvf apache-tomcat-5.5.28.tar.gz 
```
  * Setup tomcat:
    * Edit bin/startup.sh, bin/shutdown.sh and the following line at the beginning of the file :
```
		export JAVA_HOME=/path/to/java/installation
```
    * Edit conf/tomcat-users.xml, and add the following line before the closing &lt;/tomcat-users&gt; :
```
		<user username="admin" password="admin" roles="tomcat,manager"/>
```
  * You can now startup tomcat by runnnig bin/startup.sh. Tomcat starts up on port 8080.

  * Get the Hudson war file
```
	wget http://hudson.gotdns.com/latest/hudson.war
```
  * Deploy the hudson war file
```
	cp hudson.war ./tomcat-server/webapps
```
  * After few minutes Hudson should be available on http://localhost:8080/hudson


# Setting up Selenium RC

## Folder structure for tests

The following is the folder structure for the tests

```
tests
|-- bin
|   |-- ant
|   |   |-- bin
|   |   |-- docs
|   |   |-- etc
|   |   `-- lib
|   `-- selenium-server
|       |-- selenium-dotnet-client-driver-1.0.1
|       |-- selenium-java-client-driver-1.0.1
|       |-- selenium-perl-client-driver-1.0.1
|       |-- selenium-php-client-driver-1.0.1
|       |-- selenium-python-client-driver-1.0.1
|       |-- selenium-ruby-client-driver-1.0.1
|       `-- selenium-server-1.0.3
|-- dbdumps
`-- java-tests
    `-- selenium-tests
        |-- build
        |-- lib
        |-- nbproject
        |-- src
        `-- test
```

We start by preparing the folder structure

```
mkdir tests
mkdir tests/bin
mkdir tests/java-tests
```

## Installing Selenium

Download & Install Selenium into tests/bin/selenium-server :
```
	wget http://selenium.googlecode.com/files/selenium-remote-control-1.0.3.zip
	unzip selenium-remote-control-1.0.3.zip -d tests/bin/selenium-server
```

## Installing Ant

Download & Install ant into tests/bin/ant :
```
	wget http://apache.osuosl.org/ant/binaries/apache-ant-1.8.0-bin.tar.gz
	mkdir tests/bin/ant
	tar -C tests/bin/ant -xvf apache-ant-1.8.0-bin.tar.gz
```

# Using the Selenium tests


## Pre-requisites

  * Install firefox
```
	sudo apt-get install firefox
```
  * Install Xvfb, virtual frame buffer (since our Linux box does not have gdm / or a window manager running)
```
	sudo apt-get install Xvfb
```


## Running the tests

  * Get the Selenium tests from svn
```
	rm -rf tests/java-tests/selenium-tests
	svn export http://bungeni-portal.googlecode.com/svn/BungeniTesting/java/selenium-tests tests/java-tests/selenium-tests
```
  * Start Xvfb with a appropriate display property
```
	Xvfb :99 -ac &
```
  * Start Firefox
```
	export DISPLAY=:99
	/usr/lib/firefox-3.0.18/firefox &
```
  * Start the selenium server
```
	java -jar tests/bin/selenium-server/selenium-server-1.0.3/selenium-server.jar &
```
  * Run the selenium tests & note the errors
```
	cd tests/java-tests/selenium-tests && ../../bin/ant/bin/ant -Dtest-sys-prop.test.props=`pwd`/src/resources/runtime.properties test
```
  * Shutdown selenium server
```
	wget -O result.txt  http://localhost:4444/selenium-server/driver/?cmd=shutDownSeleniumServer
```
  * Shutdown firefox
```
	killall firefox
```
  * Shutdown Xvfb
```
	killall Xvfb
```

## Automated running of tests

All the above tests can be run via shell scripts :
```
	rm tests/*.sh tests/*.path
	rm -rf tests/startup_scripts
	cd tests && svn export http://bungeni-portal.googlecode.com/svn/BungeniTesting/java/startup_scripts/ 
	cp startup_scripts/*.sh ..
	cp startup_scripts/*.path ..
	cd ../..
```
Edit the file `firefox.path` appropriately to point it to the firefox binary and then run :
```
	cd tests && ./runall.sh
```

# Setup the Automated Build & Test task on Hudson

  * Add a build task for bungeni
    * Add Build script & notification parameters
  * Add a test task for Bungeni
    * Add Build script & notification parameters
  * Add a cron schedule