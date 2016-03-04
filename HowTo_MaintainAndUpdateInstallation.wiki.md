# Introduction

This page documents typical maintenance tasks for a bungeni installation



# Switching host ips

You setup the system with `app_host` set to local host, but now want to make the system available over a public IP.

> Change the `app_host` parameter to the new IP and run :
```
fab -H <hostname> config_ini:bungeni config_ini:plone config_ini:portal config_supervisord
```


# Update source

You want to update the source code for the installation.

> Run the following to update the source bungeni, plone and portal. You can also run just a specific action to update the source of just one of bungeni, plone or portal :
```
fab -H <hostname> bungeni_update plone_update portal_update
```
> Occasionally after you update the source you will need to run a 'buildout' for the system to recognize your changes. You can execute app specific buildouts :
```
fab -H <hostname> bungeni_build_opt plone_build_opt portal_build_opt
```
> Note that the above runs the buildout in optimistic mode (i.e. buildout -N) to run a full buildout you will need to run :
```
fab -H <hostname> bungeni_build plone_build portal_build
```

# Load Demo Data

You want to load demo data onto your bungeni installation.

> There are 3 kinds of demo data setups available :
    * large - this is a large data set with many users
```
fab -H <hostname> db_load_largedata
```
    * regular - this is a small data set with 1 user of each role
```
fab -H <hostname> db_load_demodata
```
    * minimal - this data set has only the admin user and preliminary metadata (no parliaments)
```
fab -H <hostname> db_load_mindata
```
> These commands will automatically start and stop the bungeni, portal and plone services during the loading process.