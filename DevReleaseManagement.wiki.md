# Current Scenario

## Build System

Presently we use a build system to check-out bungeni source code and build it from source on the target computer.The build system also installs and compiles required packages on the target computer. The current build system is standardized for a debian based distribution, though it can also be configured to use other kinds of distributions. The build system is suitable for developer setups and deployments and not ideally suitable or user-friendly for deployments.

## Updates

The current system for managing updates is very basic :
  * bungeni , portal and plone are pegged to specific svn revisions
  * subversion is used to update the source code base to specific revisions

### Deficiencies

This methodology is fine for developer scenarios, but it doesnt handle the following aspects of release management :

#### schema changes

Schema changes are not currently handled between releases, there is no formalized system of patches to upgrade existing data. Existing data may be corrupt between updates.
Additionally if the data had a visible aspect even ui.xml configurations can be impacted.

#### role & permission changes

Some roles and permission definitions come packaged in the system, and they are part of source code, and they are refered to in configuration e.g. by the workflows and the ui configuration. Changing the names of these roles and their definitions in source code, will most likely break existing installations.
Note that, some permissions which are part of source code (for e.g. bungeni.core.workflows/permissions.zcml ) are modified based on workflow configuration, this will be problematic if the workflow config is changed.

#### template changes

Templates are part of source code, a change to a template can result in a broken ui. e.g. if a change in a template results in a css selector being changed, it can break the theme being used by existing bungeni installations.

#### action changes

some aspects of action changes example contextual menus on a item are part of source code and they refer to permissions related to an item e.g. in bungeni.ui/menu.zcml
any changes to such source configuration (perhaps by changing the permission or modifying the menu items themselves ) can impact an existing installation in unknown ways.


#### workflow & workspace changes

The workflow / workspace system is configured via xml files in bungeni\_custom. An update to the workflow system in Bungeni can result in a new element / attribute to be required or the removal of an existing attribute / element. Such updates will likely break existing installations.

#### translations

changes to existing message ids in the system will break all existing installations (unless the translations were correspondingly updated).


# Model Scenario

## Deployment System

The current build system needs to be part of a larger deployment mechanism.  The current build  incorporates release pegs to move between tested source-code versions, but doesnt manage other aspects of release management like safe-upgrades to configuration and data.

An ideal scenario is where a user installs bungeni with :

```
sudo apt-get install bungeni 
```

or

```
sudo apt-get  install bungeni-legislative bungeni-plone bungeni-deliverance 
```

and then safely updates existing installations with :

```
sudo apt-get upgrade bungeni
```

the debian packaging provides a wrapper for a deployment mechanism - this doesnt mean the current build system will go away, the packaging system has entry points into the build so it runs the appropriate install and update commands which we run manually now.

So for developer / system level users the build mechanism will still be accessible directly (as now) and for deployment users the packaging syustem is the method of access.

This also implies that there be formal published releases for Bungeni with a known and tested upgrade path between releases, so that anyone can install a specific release and upgrade up the formal release chain.

It goes without saying that any upgrade requires that a backup be taken first prior to running an upgrade.

## Managing Updates

### Source code

for source code and build version control within a release, the current subversion mechanism will suffice.

### Configurations

configurations are currently in bungeni\_custom, while this currently version controlled, the mechanism exists to switch to an alternative customization folder by modifying the bungeni\_custom.pth file in the buildout folder.

Any live deployment, should use a branch of bungeni\_custom specifically for that live deployment and not the trunk e.g. :

bungeni\_custom/trunk <== used by the bungeni development team for testing and demo workflows
bungeni\_custom/branches/parliament\_ke <== branch used by a parliament to capture their workflows

For security reasons it may not be suitable to keep a parliament's customization config in a public repository, so a private repository is recommend for storing parliament specific configuration. In such cases additional information will be required for the configuration to identify which specific release is associated with the configuration.

Some specific configuration management aspects are described below in terms of how things could be done better - but a holistic view must taken for any "upgrade" since configurations are interconnected, and hence even the changes and their impact is interconnected.

#### schema changes

A **tested** upgrade script must be provided to upgrade existing databases safely with data from the previous release to the current release.
This script will be run by the build when running an upgrade from a origin release to a target release.

#### role & permission changes

It is perhaps better to freeze the roles that come packaged into the system -- this will reduce the possibility of breakages for cases where sub-roles, workflows, etc which refer to the role in custom configuration.

The permission definition currently part of source code bungeni.core.workflows/permissions.zcml should be written to a folder within custom configuration instead of version controlled source, since it is directly generated from workflow configuration.


#### template changes

Template changes must be very carefully done - if it is known that a template change may break css or a theme , the upgrade process must state clearly what the implication of the change is and what the possible resolution could be.

#### workflow & workspace changes

Any changes to the structure of configuration between releases must be upgraded by a upgrade script, which will be invoked up the build process during update.

#### translations

Translations need to be updated in sync with source.


### Testing and Releasing

Making stable releases with upgrade paths is an intensive activity involving :

  * 1=continuous integration testing - using a combination of tools like :
> > - Jenkins/Hudson - to monitor build stability and breakages
> > - Selenium - to identify functional failures
> > - Unit test - to identify code failures


  * 2=documentation of releases :
> > - What changes are part of a release
> > - What functionality may have been affected - (in terms of regression or improvements )
> > - Deprecations
> > - New features

  * 3=creating upgrades : this is perhaps the most involved activity.
> > - Keep track of changes to bungeni,portal and plone code bases
> > - Identify a stable source release - lets say we release a new upgrade every 3 months, an upgrade is not going to update to TRUNK but to a specific identified combination of source code revisions that is usable and safe.
> > - Create a comprehensive  upgrade script for configuration and schema changes : by sequentially keeping track of changes, an upgrade script must take data and configuration from one working combination to another working combination without any breakages or loss of data.
> > - Package an upgrade into a debian package release, and test on live simulations before release. Only well tested upgrades can result in a "release".
















