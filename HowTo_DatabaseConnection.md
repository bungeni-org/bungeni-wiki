## Introduction

You usually never need to configure the database connection.

However if you are deploying in a situation where the database server is on a different server from the Bungeni server you can change the database connection string used by Bungeni

## Warning

This configuration change can disable your system if done incorrectly and is intended only for customized deployments. Please be very sure of what you are doing before attempting this change.

## Changing the database connection string

Open the file `sys/db.zcml` in your [customization folder](http://code.google.com/p/bungeni-portal/wiki/HowTo_CustomizeParliamentaryInformationManagement) in a text editor .

The file will look like this :
```

<?xml version="1.0"?>
<!--
Specify the db connection string here
-->
<configure xmlns="http://namespaces.zope.org/zope"
    xmlns:db="http://namespaces.objectrealms.net/rdb"
    >
    
    <db:engine name="bungeni-db" 
        url="postgres://localhost/bungeni"
        echo="false"
    />

</configure>
```

You can change the database connection string by editing these lines :

```
   <db:engine name="bungeni-db" 
        url="postgres://localhost/bungeni"
        echo="false"
    />
```

For e.g. --

if your postgresql server is running on a server called `bungeni-pg-server` then you will change the connection string as follows :

```
   <db:engine name="bungeni-db" 
        url="postgres://bungeni-pg-server/bungeni"
        echo="false"
    />
```


if your postgresql server is running on a server called `bungeni-pg-server` and you have changed the Postgresql port to 9847 :

```
   <db:engine name="bungeni-db" 
        url="postgres://bungeni-pg-server:9847/bungeni"
        echo="false"
    />
```


if your postgresql server is running on a server called `bungeni-pg-server` and you have changed the Postgresql port to 9847, and not used the default bungeni service user context and used a user called `pguser` with password `mypassword` :

```
   <db:engine name="bungeni-db" 
        url="postgres://pguser:mypassword@bungeni-pg-server:9847/bungeni"
        echo="false"
    />
```

if your postgresql server is running on a server called `bungeni-pg-server` and you have changed the Postgresql port to 9847, and not used the default bungeni service user context and used a user called `pguser` with password `mypassword` and you have changed the  `bungeni` database name to parliamentdb :

```
   <db:engine name="bungeni-db" 
        url="postgres://pguser:mypassword@bungeni-pg-server:9847/parliamentdb"
        echo="false"
    />
```
