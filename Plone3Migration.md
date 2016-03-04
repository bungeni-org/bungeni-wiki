## Introduction

The first bugfix release of Plone 3 is due out within a week or two. We
would like to continue developing Bungeni on the latest greatest Plone
version as soon as this is practical. This page outlines a roadmap for
migration.


## Details

There are four main stages to migration:
  * Audit our dependencies. If there are any Bungeni dependencies that don't work well on Plone 3, we have three possibilities:
    * see if we can dump them (PloneHelpCenter comes to mind), or
    * replace them with alternatives,
    * or contribute fixes and migration code, either inhouse or outsourced.
  * Audit Bungeni itself, to make sure it runs OK on Plone 3.
  * Identify and review new features introduced by Plone 3 that we need to make use of in Bungeni.
  * When Bungeni runs on Plone 3, it will log deprecation warnings. Deprecated code will keep on working until the next major release, but should be updated as time allows.


## Keeping track

During the audit and review process, please log bugs and feature requests at the [issue tracker](http://code.google.com/p/bungeni-portal/issues/list).

Use proper labels.