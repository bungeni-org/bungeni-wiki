


# Pre-Requisites

You should have completed the following steps :

  * **[How to setup the Pre-requisites](Install_PreRequisites_Fabric.md)**
  * **[How to setup Bungeni Parliamentary Information System](Install_Bungeni_Fabric.md)**
  * **[How to use Supervisor Service Manager](HowTo_SupervisorServiceManager.md)**

  1. **The eXist-db build is an optional deployment configuration which adds an XML documents build repository and framework files into Bungeni.**
  1. **The eXist-db currently works on Ubuntu 10.04 +**

# Download & Setup eXist stack

See [Executing Fabric actions](http://code.google.com/p/bungeni-portal/wiki/HowTo_SetupFabricScripts#Executing_fabric_actions)

## eXist stack pre-requisites
The XML repository is updated by Gluescripts which implement Rabbit MQ, a messaging broker (see [Rabbit MQ](http://www.rabbitmq.com/)). Presetup installs all dependencies required prior to installing the stack. Use `config_supervisord` command to regenerate the configuration templates especially when updating from an existing Bungeni installation.

```
fab -H <host-name-or-ip> exist_presetup config_supervisord
```

## Installing Rabbit MQ

```
fab -H <host-name-or-ip> rabbitmq_install
```

## Installing Gluescripts

The Gluescripts subscribe to `bungeni_serialization_output_queue` for serialized documents from Bungeni workflow; [XSLT](http://en.wikipedia.org/wiki/XSLT) is applied on the documents and once transformed, pushed into the XML repository. This script runs as "exist-sync" service on [Supervisor](HowTo_SupervisorServiceManager.md).

```
fab -H <host-name-or-ip> glue_install
```

## XML Repository

eXist-db is an open source database for storing XML documents (see [eXist-db.org](http://www.exist-db.org/exist/index.xml)).

### Installing eXist-db
```
fab -H <host-name-or-ip> exist_install
```
This installs the eXist-db build.

### Installing demo data
```
fab -H <host-name-or-ip> exist_load_demodata
```

### Installing the framework files
```
fab -H <host-name-or-ip> exist_fw_install
```
This installs the eXist framework files for browsing the Bungeni XML repository.



Alternatively, if you are running Bungeni on localhost, you can run:
```
./fl exist_presetup config_supervisord rabbitmq_install glue_install exist_install exist_load_demodata exist_fw_install 
```

This will install the entire stack and load some demo data into eXist-db altogether.

# Starting and Stopping exist

See [using supervisor to start / stop eXist](http://code.google.com/p/bungeni-portal/wiki/HowTo_SupervisorServiceManager#exist)

# Starting and Stopping rabbitmq

See [using supervisor to start / stop rabbitmq](http://code.google.com/p/bungeni-portal/wiki/HowTo_SupervisorServiceManager#rabbitmq)

# About BungeniXQFramework

see [Bungeni XQ Framework Documentation](http://code.google.com/p/bungeni-exist/wiki/BungeniXQFramework)

# Next Steps

  * **[How to setup Plone Content Management System](Install_Plone_Fabric.md)**
  * **[How to setup Deliverance Portal](Install_DeliverancePortal_Fabric.md)**
