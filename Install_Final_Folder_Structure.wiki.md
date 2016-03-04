# Bungeni Installation Folder Structure

```
./bungeni_apps		# Contains the complete installation
|-- bungeni		# Contains the bungeni installation	
|   |-- bin		# Executable command line tools for bungeni
|   |-- cache		# For use by chameleon caching engine
|   |-- data		# Contains database setup scripts
|   |-- develop-eggs	# Contains python eggs deployed by buildout
|   |-- eggs		# Contains downloaded python eggs from the package server
|   |-- locales		# Custom locales used in bungeni
|   |-- logs		# Postgres Log (redundant)
|   |-- parts		# Contains postgresql, xapian indexes, xml_db output folder
|   |-- plone		# Contains the Plone installation
|   |-- portal		# Contains the Portal installation
|   |-- src		# This is the source code packages checked out by the buildout
|   |-- templates	# Documentation templates and scripts for the workflow documentation
|   `-- testdatadmp	# database dump with demo data + setup minimal metadata
|-- config		# contains supervisord.conf Supervisor process monitor config file
|-- logs		# All application logs
|-- pid			# process id files
`-- python26		# Python installation used by bungeni, plone, portal
    |-- bin
    |-- include
    |-- lib
    `-- share


```