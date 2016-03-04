The Bungeni application is a conglomeration of different specailized applications that provide specific services.

Bungeni encompasses :
  * Parliamentary Information Management and Workflow
  * Content Management
  * Archival and Retrieval

These services are provided by different applications - but visually the end-user is provided a unified visual experience by using a application proxy.

The architecture of the Bungeni application is expressed by this diagram --

![http://bungeni-portal.googlecode.com/files/bungeni_system_components.png](http://bungeni-portal.googlecode.com/files/bungeni_system_components.png)

The different application components providing this services are listed below --
  * Bungeni Parliamentary Information System - Parliamentary Information Management and Workflow
  * Plone Content Management System - Web Content Management
  * eXist XML UI - Archival and Retrieval
  * Deliverance application proxy - Unified presentation layer and application proxy.

The installation instructions here describe how to install all the components.
Certain pre-requisites need to be set-up for installing all three.

**NOTE** (fabric is intended to replace the older capistrano method of installation):
The recommended way to install the full application stack is to use the fabric installation scripts.

  * **[How to setup the Pre-requisites](Install_PreRequisites_Fabric.md)**
  * **[How to setup Bungeni Parliamentary Information System](Install_Bungeni_Fabric.md)**
  * **[How to setup Plone Content Management System](Install_Plone_Fabric.md)**
  * **[How to setup eXist XML UI](Install_eXistdb_Fabric.md)**
  * **[How to setup Deliverance Portal](Install_DeliverancePortal_Fabric.md)**
  * **[How to use the Supervisor Service Manager](HowTo_SupervisorServiceManager.md)**
  * **[How to maintain and update a Bungeni installation](HowTo_MaintainAndUpdateInstallation.md)**

