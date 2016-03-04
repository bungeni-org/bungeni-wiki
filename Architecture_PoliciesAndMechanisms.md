## Policies and Mechanisms

### General Policies and Mechanisms

#### Localization (i18n)

For localization it is used the _zope.i18n_ library. This package provides a way to register translations files for domains and languages. From python code it is used as:

```
from zope.i18nmessageid import MessageFactory

_ = MessageFactory = MessageFactory(’bungeni.ui’)

...

title = _(u’label_translate’, default=u’Language:’)
```


or

```
from zope.i18n import translate

...

text = translate(self.body)
```

See the Multilingual paragraph for use in page templates and the format of translations file.

#### Multilingual UI / templating

The multilingual feature of UI (the ability to translate the User interface) is provided by the i18n feature integrated in the Zope Page Template system. For example in bungeni/ui/viewlets/templates/whatson.pt there is:

```
<div metal:fill-slot="body" i18n:domain="bungeni.ui">

<h1> <a href="/business" i18n:translate="">What’s on</a></h1>
```

_i18n:domain_ specifies the translation domain because the same string can be present in different domains. The _i18n:translate_ command instruct the engine to translate the text in the tag based on the browser language. The translations are provided from “.po” file stored in the software packages, for example for bungeni.ui there is the folder locales/en/LC\_MESSAGES containing the domain bungeni.ui.po and the compiled version bungeni.ui.mo. The format of files is very simple, it contains a metadata part and then the translations in the form of (message id, message string) couple:

```
"Project-Id-Version: \n"

"POT-Creation-Date: Thu Jan 14 13:13:54 2010\n"

"PO-Revision-Date: 2010-01-14 13:26+0300\n"

"Last-Translator: Christian Ledermann <christian@parliaments.info>\n" "Language-Team: \n"

"MIME-Version: 1.0\n"

"Content-Type: text/plain; charset=UTF-8\n"

"Content-Transfer-Encoding: 8bit\n"

#

#: src/bungeni.ui/bungeni/ui/audit.py:20

msgid "action"

msgstr "Action"

...
```

As stated above the templating system is based on Zope Page Templates, refer to http://zpt.sourceforge.net/ for an overview of the software. The main feature of this templating system is the separation between the namespace of commands and the namespace of html, this way the html is still valid and the TAL (Template Attribute Language) commands aren’t interfering with him, for example:

```
<title tal:content="here/title">Page Title</title>
```
where_tal:content_ rewrite the tag text. For further explanation: http://docs.zope.org/zope2/zope2book/ZPT.html.

#### Storage

For storing the data of BungeniPortal it was choosed the well know RDB Postgres. The BungeniCMS instead use the usual setup of Zope with ZODB, a tested Object Oriented database.

#### Search

BungeniPortal use Xapian in collaboration with Postgres to provide the full-text search feature. BungeniCMS use the internal catalog with standard indexes.

#### Security

Both BungeniPortal and BungeniCMS are based on Zope, it used a strong authentication/authorization system based on permissions and roles. The programer can assign a permission to a function or a method of a class using zcml configuration or (as in Plone) with the functions declareProtected, declarePrivate and declarePublic. After that the programmer create a set of roles and assign the permissions to roles, then the roles are assigned to users and groups, either in zcml or through the ZMI interface in Plone (the setup is stored on file system). The publisher of Zope checks the user permissions before rendering a page or calling a method, raising an Unauthorized exception in case the user hasn’t the required permission.

#### Information Architecture

The information architecture is very parliament specific, in the section 2.3.1 there is shown a base implementation.

#### System management and monitoring.

Supervisord is used to manage the servers composing the software (status, start, stop, restart). For which we know there is not a system like monit, Munin or Nagios to monitor the load of the system, the status of servers and automatically restart the daemons in case they are not responding.

### Policies and Mechanisms for Quality Requirements.

There are no polices nor mechanisms for quality requirements. We leave here the sub-chapter titles for future writings.

#### Developer-Oriented Quality Mechanisms (extensibility, portability, re-usability, etc.)

#### Customization/personalization

#### Security

#### Performance