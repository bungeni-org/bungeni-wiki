## Software Architecture Overview

### Scope

The scope of this document is to help a developer to understand the deployment configuration and to provide an understanding of how to modify the existing functionalities and to create new ones. The system is composed of many different modules and each requires the knowledge of the used technology, the document will not try to explain all but only provides a global vision.

### Description

To describe the software the UML notation will be used and some snippets of code will be provided : bash, python, ruby and some specific configuration languages for Paster and Deliverance.

### Logical Views

#### Logical elements

The main logical elements are described below:

  * Theming component: provides a coherent look-and-feel to BungeniPortal and BungeniCMS.

  * Dispatcher redirects the incoming requests to the correct URL inside the main applications.

  * BungeniPortal provides the parliament functionalities.

  * BungeniDB is a relational DB, it stores the data of BungeniPortal.

  * BungeniCMS contains the general materials, it is the content management part of the system. BungeniCMS uses an integrated object DB (not shown in the diagram).

  * Static resources are the images and css files which are served straight from file system.

Diagram 1 : Logical Elements

![http://bungeni-portal.googlecode.com/files/swad_html_645a160c.png](http://bungeni-portal.googlecode.com/files/swad_html_645a160c.png)

##### Theme Delivering

This component provides a coherent look-and-feel across all the applications in the site. The html coming from a source (e.g. BungeniPortal or BungeniCMS) is re-written based on a ’theme’ which is a static html page: the component extract the parts from the page and fill the empty spaces in the static template. This operation is done using a set of rules based on a XPath syntax.

##### Dispatcher

This component simply call the application using a mapping between urls and apps.

##### BungeniPortal

This is the application that provide the specific parliament features, it can be break up in the following sections:

  * Business: in this area there are the daily operations of the various parliament activities

  * What’s on: an overview of the daily operations of the parliament.

  * Committees: list of committees, for each one there is metadata about the matter of discussion, membership and sittings.

  * Bills: list of bills and metadata for each bill. Actions are provided to version the bill and access the workflow associated with the bill.

  * Questions: list of questions and associated metadata. Workflow and versioning actions are provided.

  * Motions: list of motions and associated metadata. Workflow and versioning actions are provided.

  * Tabled documents: list of tabled documents and metadata. Workflow and versioning actions are provided.

  * Agenda items: list of agenda items and metadata.

  * Sittings: calendar showing the sittings of the plenary and the committees.

  * Parliamentary publications: list of publications and informations, these publications are the reports of sittings.

  * Members: here one can search for information about members of parliament:

  * Member of parliament: general information such as name and election date.

  * Personal Info: a complete biography of the member.

  * Offices held: informations about offices in which the member has a title.

  * Parliamentary activities: a list of content workflows the member has Participated in. e.g. questions created by the member or motions moved by the member.

  * Archive: access to current and historical activities of the parliament, the categories are

  * Parliaments

  * Political groups

  * Committees

  * Governments

  * Workspace: This is available for members of parliament and for clerks. This provides access to the most relevant and current information for the user in a single page e.g. for the Member of Parliament – the following tabs of information are provided – “To Do”, “Draft Items” , “In Progress” and “My Groups”.

  * Administration: This is an administration section provided to the Admin. This is used for adding parliaments, new users, closing parliaments, entering preliminary metadata etc.

The following diagram show the logical components of the BungeniPortal part:

Diagram 2 : Logical Components of BungeniPortal

![http://bungeni-portal.googlecode.com/files/swad_html_3d601ca7.png](http://bungeni-portal.googlecode.com/files/swad_html_3d601ca7.png)

The versions and workflow functionalities provide traversals into the content; for example in a motion there are the links to past workflow states and older versions of the motion – allowing the user to browse not just the current state of the motion but also the full audited history of the motion.

From some sections a user can reach contents in other sections; for example from the tab ’Parliamentary activities’ of a member it is possible to reach a bill moved by the member.

##### BungeniCMS

This is the content management system part of the portal, it provides a set of functionalities which are designed to:

  * Allow a large number of people to contribute to and share stored contents

  * Control access to contents, based on permissions.

  * User roles or group membership define what each user can do (not only edit and view).

  * Improve communication between users using comments on contents.

  * Publication workflow and versioning support

The CMS can contains various contents: documents, events, news, pictures, files are the main types.

The information architecture is organized in a tree structure, at this moment it looks as:

  * How we work

  * Rules and regulations

  * How parliament works

  * Seating plan

  * Administrative

  * Reference material

  * History of parliament

  * Online resources

  * Useful links

  * Picture gallery

  * Have your say

  * Vote in the election

  * Become an member of parliament

  * Present a petition

  * Visit parliament

This is the base structure but subject to changes due to the specificity of each parliaments.

#### Logical relationships

The following diagram shows how the different parts of the system communicate with each other:

Diagram 3 : Logical Relationships

![http://bungeni-portal.googlecode.com/files/swad_html_10e20515.png](http://bungeni-portal.googlecode.com/files/swad_html_10e20515.png)

The request is passed from the ’theming component’ to the dispatcher that call the designated application; the returned html is processed from the ’theming component’ and release to the user. In this diagram is missing the ’paster serve’ component that provides main access to the web server and manage the wsgi messages among the parts. As shown the components are for the most not dependent upon each other: the ’theming component’ and the Dispatcher merge backend applications, the BungeniCMS can work without the others as the BungeniPortal (in this case however there is an explicit need for the RDBMS to store and retrive data).

#### Deployment of logical elements to hardware components

The starter point is the supervisord configuration: supervisord.conf (a file with structure similar to Microsoft Windows INI files). From this file you can see which services compose the system and how they are started:

![http://bungeni-portal.googlecode.com/files/swad_html_m8a7a33.png](http://bungeni-portal.googlecode.com/files/swad_html_m8a7a33.png)
Diagram 4 : Deployment

The sections are:

  * program:portal

  * program:plone

  * program:bungeni

  * program:postgres

  * program:openoffice

##### program:portal

Specify how all web servers and applications are reachable, they are served through Paster. Paster is a two-level command and the second level is pluggable, for Bungeni ’serve’ that the ’serve’ command is used, which is a script to ’serve’ applications based on the WSGI interface (similar to CGI) using the http protocol (see http://pythonpaste.org/script/developer.html). The configuration of ’portal’ is in portal/deploy.ini: the ’main’ section defines a pipeline which filters requests through deliverance and serving a lot of urls: see [pipeline:main] then [filter:deliverance] and [composite:dispatch] sections. Deliverance provides a uniform theme to all applications (http://deliverance.openplans.org/index.html), it intercepts the pages and via a set of rules applies a common look-and-feel. In the ’dispatch’ section you can see the url mapped:

  * / = plone

  * /plone = plone

these are provided from server specified in [program:plone] of supervisord.conf

  * /bungeni = bungeni

  * /members = members

  * /business = business

  * /business/whats-on = whatson

  * /archive = archive

  * /calendar = calendar

  * /admin = admin

  * /workspace\_archive = workspace\_archive

these are provided from server specified in [program:bungeni] of supervisord.conf

  * /static = static

this is provided directly as a wsgi service from the module bungeni.portal#static

##### program:bungeni

Bungeni Portal is served through paster with deploy.ini, the mapped urls are ’/’ and ’/cache’. On ’/’ there is the real portal, a pipeline of repoze.who (WSGI authentican middleware) and bungeni.server (code in src/bungeni.server). This one uses ore.wsgiapp and site.zcml (in the current folder). ore.wsgiapp allows one to bootstrap a zope3 environment as a wsgi application without a ZODB and site.zcml is the zope 3 instance configuration.

The names provided from bungeni sections are managed from bungeni.ui: the names/urls are implemented as browser pages or menu actions: see the configuration in src/bungeni.ui/bungeni/ui/menu.zcml and bungeni.ui/bungeni/ui/views.zcml (this requires understanding of zcml zope technology).

##### program:plone

BungeniCMS is based on Plone and it is served through paster (that is unusual for plone) with the configuration file plone/etc/deploy.ini The paster configuration is a pipeline of various middleware at end of which there is Zope2. The BungeniCMS is the ’site’ instance of Plone in the root of Zope.

##### program:postgres

The configuration to start up postgres DB.

### Source Code Views:

#### Main Components

##### Capistrano

Capistrano is deployment system written in Ruby. It is a tool for automating tasks on one or more remote servers. It executes commands in parallel on all targeted machines, and provides a mechanism for rolling back changes across multiple machines. It is ideal for anyone doing any kind of system administration, either professionally or incidentally.

##### Supervisord

The supervisord is a client/server system that allows its users to control a number of processes on UNIX-like operating systems. It is responsible for starting programs at its own invocation, responding to commands from clients, restarting crashed or exited subprocesseses, logging its subprocess stdout and stderr output, and generating and handling "events" corresponding to points in subprocess lifetimes. It provides a web user interface to view and control process status.

The above two components are part of the deployment system.

##### Paster

Python Paste is a set of libraries to deploy WSGI applications, it covers all aspect of a CGI application: testing, dispatching, authentication, debugging and deployment. Specifically the Paste Deployment is a system for finding and configuring WSGI applications and servers, it provides a single, simple function (loadapp) for loading a WSGI application from a configuration file or a Python Egg. The usual way to deploy a WSGI application is to use ’paster serve’, this is the command line counterpart to serve an application using a ’Paste Deploy’ configuration file.

##### Deliverance

Deliverance is an integration tools, to allow web applications from different frameworks or languages to be integrated gracefully. Deliverance provides a common look-and-feel across all the applications in the site. It takes the html from a source then apply a ’theme’ to the html using something similar to xslt transforms (but without the resctrictions).

##### Zope 2 and 3

Zope is an open source web application server primarily written in the Python programming language. It features a transactional object database which can store not only content and custom data, but also dynamic HTML templates, scripts, a search engine, and relational database (RDBMS) connections and code. It features a strong through-the-web development model, allowing you to update your web site from anywhere in the world. To allow for this, Zope also features a tightly integrated security model.

Zope 3 is a rewrite by the Zope developers of the Zope web application server (Zope3 is now named BlueBream). The project try to create a more developer-friendly and flexible platform for programming web applications. The original intent of Zope 3 was to become a replacement for Zope 2, however this did not happen as planned. Instead Zope 2 continued to make up the majority of new Zope deployments, mostly due to the popularity of Plone. At this moment many components of Zope 3 are used in Zope 2 and in other frameworks, and the community use the term ZTK (Zope Tool Kit) to define a set of libraries used as base for Zope3, Zope2, bfg and Grok frameworks.

The main innovation of Zope 3 is the ’component architecture’, which allows  structuring code into small, composable units with introspectable interfaces, configurable through the ZCML files.

##### Postgres

PostgreSQL is a powerful, open source object-relational database system. It has more than 15 years of active development and a proven architecture that has earned it a strong reputation for reliability, data integrity, and correctness. An enterprise class database, PostgreSQL boasts sophisticated features such as Multi-Version Concurrency Control (MVCC), point in time recovery, tablespaces, asynchronous replication, nested transactions (savepoints), online/hot backups, a sophisticated query planner/optimizer, and write ahead logging for fault tolerance.

##### SQLAlchemy

SQLAlchemy is the Python SQL toolkit and Object Relational Mapper that gives application developers the full power and flexibility of SQL. It provides a full suite of well known enterprise-level persistence patterns, designed for efficient and high-performing database access, adapted into a simple and Pythonic domain language. SQLAlchemy doesn’t view databases as just collections of tables; it sees them as relational algebra engines. Its object relational mapper enables classes to be mapped against the database in more than one way. SQL constructs don’t just select from just tables—you can also select from joins, subqueries, and unions. Thus database relationships and domain object models can be cleanly decoupled from the beginning, allowing both sides to develop to their full potential.

##### Xapian

Xapian is an Open Source Search Engine Library, it is a highly adaptable toolkit which allows developers to easily add advanced indexing and search facilities to their own applications. It supports the Probabilistic Information Retrieval model and also supports a rich set of boolean query operators.

##### Plone

Plone is a powerful, flexible Content Management solution that is easy to install, use and extend. Plone lets non-technical people create and maintain information using only a web browser. The main use of Plone is as base for web sites or intranets because its modular nature helps the customization of all aspects. Plone is a product for Zope2 application server, so it shares the core functionalities as components architecture, security and scalability.

#### Theming Component & Dispatcher

##### Theming component

The system use Deliverance to add a common theme to BungeniPortal and BungeniCMS. Deliverance is integrated in the _paster_ middleware, so it is a WSGI application. Usually this type of applications are referred as pipeline components. Deliverance receives the responses from the applications mapped in the _dispatch_ section then transform the html on the base of the rules.xml file. In portal/deploy.ini there is:

```
[pipeline:main]

pipeline = deliverance
```

dispatch

This paster application is launched from _supervisord_, see supervisord.conf in the section [program:portal].

The configuration of Deliverance section is:

```
[filter:deliverance]

use = egg:bungeni.portal#deliverance

## use rule_file_host here since thats the internal server:port for deliverance rule_uri =

http://%(rule_file_host)s/static/themes/rules.xml

```

The paster configuration “egg:bungeni.portal#deliverance” is related to the declaration in the setup.py of bugeni.portal egg:

```
entry_points = """

[paste.filter_app_factory]

deliverance =

bungeni.portal.middleware:make_deliverance_middleware

[paste.app_factory]

static = bungeni.portal.app:make_static_serving_app

""",
```

_make\_deliverance\_middleware_ is the factory method generating the real Deliverance app. When instantiated the rule\_uri parameter is passed to the factory.

##### Dispatcher

“egg:Paste#urlmap” it is a standard component of the Paste framework. It maps the urls to applications providing the same features of a rewrite rule o proxy rule in Apache. For more informations about “urlmap” refer to: http://pythonpaste.org/deploy/

#### Bungeni Portal

This diagram shows the main components of BungeniPortal:

Diagram 5 : Packages in BungeniPortal

![http://bungeni-portal.googlecode.com/files/swad_html_54f027df.png](http://bungeni-portal.googlecode.com/files/swad_html_54f027df.png)

##### bungeni.server

The application is based on Zope3; this package contains the configuration of the libraries (what is included and excluded to reduce the startup time) and the function ’application\_factory’that is used by ’paster serve’ command to launch the application; it depends on ore.wsgiapp that allows bootstraping a Zope3 environment as a wsgi application without a ZODB backend. This package contains also the utility SMTPMailer used for sending e-mail.

##### bungeni.portal

Contains configurations for the RDBMS in the shape of directives for ’ore.alchemist’ (a wrapper for SQLAlchemy) and the declaration of the base template (ploned.pt and ploned.py). Futher it defines the ’make\_static\_serving\_app’ function used by ’paster serve’ for static parts contained in the ’static’ folder and sub-folders, here there are also the base rules for Deliverance and the theme files.

##### bugeni.core

This package contains the application started from the ’application\_factory’ entry point of bungeni.server and the contents creating the sections of the portal. The following diagram shows the main classes involved:

Diagram 6 : bungeni.core interaction

![http://bungeni-portal.googlecode.com/files/swad_html_66a6c575.png](http://bungeni-portal.googlecode.com/files/swad_html_66a6c575.png)

bungeni.core.app

The main class is AppSetup that is the factory adapter for the BungeniApp (IBungeniApplication). As the name stated it setups the application:

  * create indexes for each content and add these to the ’indexer’ object that wraps Xapian (using a file system storage for the index catalog: 

&lt;buildoutpath&gt;

 /parts/index)

  * add to the application object the names bound to the functionalities. The application context is a dictionary-like object so for example the ’business’ link is added as key:
```
business = self.context["business"] = Section(

title=_(u"Business"),

description=_(u"Daily operations of the parliament."),

default_name=u"whats-on")
```

The sections are bases on four types of classes:

  * bungeni.core.content.Section: is an OrderedContainer, a Zope3 class modelling a folder in which the contents container are maintained in order. For example ’Business’, ’Members’, ’Archive’ are Section contents. Note that usually the OrderedContainers are Persistent objects (in Zope sense) but in this case they are not stored at all.

  * bungeni.core.content.QueryContent: is an object at which is attached a function performing SQL query, see bungeni.model.queries module. For example the "committees" and "bills" under business are QueryContent:

```
business[u"committees"] = QueryContent(

container_getter(get_current_parliament, ’committees’),

title=_(u"Committees"),

marker=interfaces.ICommitteeAddContext,

description=_(u"View committees created by the current parliament."))
```

  * bungeni.ui.calendar.CalendarView: is a Browser Page providing the calendar functionalities, see bungeni.ui

  * bungeni.ui.workspace.archive.WorkspaceArchiveView: is the user/member workspace, see bungeni.ui

Below in the tree the are the contents based on bungeni.models.domain, they are the objects mapping tables and rows in the RDB and accessed through the SQLAlchemy ORM. For example the section ’bills’ is a BilllContainer, a folderish object, and contains Bill from bungeni.models.domain. Note that the ’domain.**Container’ are auto-generated from SQLAlchemy infrastructure. Here a partial diagram showing the objects and the relations with urls:**

Diagram 7 : Bungeni package integration

![http://bungeni-portal.googlecode.com/files/swad_html_184c5d.png](http://bungeni-portal.googlecode.com/files/swad_html_184c5d.png)

bungeni.core.workflows

In bungeni.core.workflows there are configurations, factories and definitions of workflows used in the site. A worflow is tied to a bungeni.model.domain content through a configuration based on the interface. As example the implementation for Bill content looks like (see bungeni.models):

Diagram 8 : Workflows in bungeni.core

![http://bungeni-portal.googlecode.com/files/swad_html_1eba1ed9.png](http://bungeni-portal.googlecode.com/files/swad_html_1eba1ed9.png)

In configure.zcml of bungeni.core.workflows for Bill there is:

```
<adapter

for="bungeni.models.interfaces.IBill"

provides="ore.workflow.interfaces.IWorkflow"

factory=".adapters.BillWorkflowAdapter" />
```

This means that BillWorkflowAdapter is the constructor of workflow for the class implementing IBill interface. The operation is done through the load\_workflow method passed to AdaptedWorkflow class (not showed in the diagram), it read the bill.xml file, containing the description of the workflow in terms of states and transitions, then generate the workflow object. In similar way the state of workflow is managed by WorkflowState class, it provides the access to the _state_ attribute in a Bill object; with this attribute the engine is able to determine the possible transitions to others states.

For an explanation about entity based workflow engines see: http://www.zope.org/Members/hathawsh/DCWorkflow_docs/default/DCWorkflow_doc.pdf and workflow.txt in http://pypi.python.org/pypi/ore.workflow package.

##### bungeni.models

The main module of this package is ’domain’, the module is rather complicated so this document reports only part of the inner classes to show the general structure. The base class is Entity: the main scope is to provide ILocation interface that is used to declare the parent and the name of the object inside this parent container. The second important class is Parliament, the root of the system, contains the containers of commitees, members, bills etc.

Below a partial view of the module:

Diagram 9 : bungeni.models

![http://bungeni-portal.googlecode.com/files/swad_html_mc13a81c.png](http://bungeni-portal.googlecode.com/files/swad_html_mc13a81c.png)

As example of implementations on Parliament object there is the ’bills’ name that is a BillContainer containing Bill objects. The Bill has two other classes associated:

  * BillVersion: it is a reference to a Bill object.

  * BillChange: it is an operation done on an object.

The tables for Bill, BillVersion and BillChange are generated in ’bugeni.models.schema’ module, e.g.:

```
bills = rdb.Table(

"bills",

metadata,

rdb.Column( "bill_id",

rdb.Integer,

rdb.ForeignKey(’parliamentary_items.parliamentary_item_id’),

primary_key=True ),

rdb.Column( "bill_type_id",

rdb.Integer,

rdb.ForeignKey(’bill_types.bill_type_id’),

nullable = False ),

rdb.Column( "ministry_id",

rdb.Integer,

rdb.ForeignKey(’groups.group_id’) ),

rdb.Column( "identifier", rdb.Integer),

rdb.Column( "summary", rdb.UnicodeText ),

rdb.Column( "publication_date", rdb.Date ),

)

bill_changes = make_changes_table( bills, metadata )

bill_versions = make_versions_table( bills, metadata, parliamentary_items )
```

The table for versions and changes are generated through the methods _make\_changes\_table_ and _make\_version\_table_, they create new tables with specific fields, in particular _bill\_versions_ contains the columns of _parliamentary\_items_ table. Below the relation between these classes (excerpt from classes and RDB table definitions):

Diagram 10 : Relation between content type tables

![http://bungeni-portal.googlecode.com/files/swad_html_495b424d.png](http://bungeni-portal.googlecode.com/files/swad_html_495b424d.png)

Each Bill object is a ParlamentaryItem in terms of RDB, this means that for each row in Bill table it is created a row in ParlamentaryItem table. If you modify a Bill object you are modifying a ParlamentaryItem row, then the old values of this record are copied in a new BillVersion row and it is generated a new BillChange row.

##### bungeni.ui

The following packages are providing the interface for various parts of the BungeniPortal:

  * forms package: content and container browser views.

  * calendar package: contains the code to manage events associated to parlamentary items.

  * workspaces.py (and other modules): manage the access to items of a member fo parliament.

  * workflow.py (and other modules): manage the interface to access the workflow functionalities.

  * versions.py (and others): provide the functionalities to access the item versions.

#### Bungeni CMS

It is a Plone installation with custom and external products installed to provide extended functionality. Some portal sections are folders but in the final integration with Deliverance they are mapped on to BungeniPortal urls. Refer to http://www.plone.org for more information about Plone. The used version is 4.1
