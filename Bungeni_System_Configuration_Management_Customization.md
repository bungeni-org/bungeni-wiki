


# Introduction

Bungeni provides various ways to configure, customize and manage the application. Presently there are user interfaces for some aspects, and none for others. The objective of this description is to identify the different points of configuration, management and customization of the system.

# Configuration, Customization and Management external to Bungeni sub-system

All the below options are accessible external to the Bungeni application and its constraints. These changes require access to the file system, and to the source of the system. It is not advisable to make these changes on a live system - but instead on a test system, since it is possible some changes could adversely affect the system (i.e. bring it down )


## System Level Management

The bungeni installation is updated and built using a system called Fabric.

Fabric is a set of python libraries which allow automation and scripting of repeated activities like :

  * Restart the system
  * Updating the system
  * Reverting changes
  * Building the system from source
  * Resetting the database
  * Taking backups
  * Restoring backups
  * etc...

### Configuration files

Fabric itself uses an ini file for configuring installation parameters.


### Access

Fabric commands are run from the terminal, and requires shell access to the system os - as it interacts with the bash interpreter and other operating system utilities like the package manager.


## Form Customization

Bungeni forms can be customized per content type in the following ways :

  * Which fields are visible/ editable to whom
  * Order of fields
  * Which fields can be shown in listings or in other specific modes (In add, view and edit modes )

### Configuration files

The forms are customized by changing an xml file called ui.xml which describes the forms, the order of fields and the visibility per field by role.

### Access

The xml file resides on the file system. Changing the file requires access to the file system


## Workflow Customization

Bungeni workflows can be customized per content type in the following ways :

  * Define / modify workflow states and grants for the states
  * Define / modify workflow transitions between states

### Configuration files

The workflows are customized by changing individual xml files named after the content types. The xml files describes, states, grants and transitions.

### Access

The xml files reside on the file system. Changing the file requires access to the file system.


## Workspace Customization

Bungeni workspaces can be customized per content type in the following ways:
  * workspace customization is defined per workflow state
  * every state defines the tabs for the workspace and what roles are applicable for the tabs

### Configuration files

The workspaces are customized by changing individual xml files named after the content types.

### Access

The xml files reside on the file system. Changing the file requires access to the file system.

## Workflow documentation

Bungeni provides a workflow documentation generator. This can be a useful tool, since the workflow can be customized.
The documentation is generate from the workflow xml files.

### Configuration files

XHTML files on the file system

### Access

The documentation is generated via a shell script. Running it requires shell access.


## Database Connection String

Bungeni uses a postgres relational database. The connection string to connect to the db is customizable. This could change if the db is deployed on a different server.

### Configuration files

XML files (in zcml format) on the file system

### Access

The zcml file resides on the file system. File system access is required to modify it.

## OpenOffice configuration

Bungeni uses openoffice for document conversion and report generation purposes. The configuration for openoffice lets Bungeni know which openoffice installation to use.

### Configuration files

XML files (in zcml format) on the file system

### Access

The zcml file resides on the file system. File system access is required to modify it.

## Default Roles and Permissions

Default roles and permissions of the system (including default grants) are configurable.

### Configuration files

XML files (in zcml format) on the file system

### Access

The zcml file resides on the file system. File system access is required to modify it.

## Themes

Bungeni supports multiple themes. Themes can be switched via the fabric scripts. Themes are packaged as customizations.

### Configuration files

XHTML, CSS and image files on the file system

### Access

The files resides on the file system. Shell access required for modifying and customizing themes.


## Translations

Bungeni supports multiple languages.

### Configuration  files

PO files on the file system

### Access

The files resides on the file system. subversion access (via fabric) required to update PO files.


## Subject Terms

Bungeni supports applying subject terms to content. The subject are a hierarchical list of items, and appear in a subject-term selector widget.

### Configuration  files

XML file (in VDEX format) on the file system.

### Access

The files resides on the file system. File system access required to update file.


# Configuration, Customization and Management internal to Bungeni sub-system

All the below options are accessible from within the Bungeni application. These changes require authenticated access to bungeni via a web browser, and the changes are made via web forms.

These settings do not use configuration files but instead store information in the same postgresql database used to store content. These configurations do not change the logical behavior of the system or the system configuration, but instead these are "business  parameters" accessible within bungeni.


## Content

Setting up of parliament, users etc.

## Settings

Notification emails, expiry dates for specific workflow actions.

## Email Settings

SMTP server settings.

## Search index settings

Single button to re-index db

## Vocabularies

Vocabularies for different item typologies in bungeni (bill type, question type etc.)


