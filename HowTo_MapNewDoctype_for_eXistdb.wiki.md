

# Introduction

The eXist-db XML repository acts as a permanent storage for documents serialized by Bungeni. A custom ontology format is used to markup, group and identify different document types in Bungeni. The markup is applied prior to uploading documents to eXist-db. The entire stack of applications that enable this is documented [here](Install_eXistdb_Fabric.md).

There are currently two configuration that map the document type during the transformation process prior to upload:
  * glue.ini.tmpl, and
  * doctype pipeline configuration

Below are the steps in order to add a new document type for publishing into the repository.


## Step 1 - _glue.ini.tmpl_

This file is located in the configuration templates folder that comes with Bungeni. Typically under
```
{BUNGENI_FABRIC_PATH}/exec/templates/
```
Each document type that will be processed is added here under the **`[pipelines]`** section. E.g.

```
...
[pipelines]
committee = configfiles/configs/config_bungeni_group.xml
sitting = configfiles/configs/config_bungeni_groupsitting.xml
user = configfiles/configs/config_bungeni_user.xml
member_of_parliament = configfiles/configs/config_bungeni_membership.xml
committee_member = configfiles/configs/config_bungeni_membership.xml
question = configfiles/configs/config_bungeni_parliamentaryitem.xml
bill = configfiles/configs/config_bungeni_parliamentaryitem.xml
...
```
Bungeni documents belong to an archtype the defines the general manner it which it is going to be processed and markedup. Examples
  * `member_of_parliament`, `office_member`, e.t.c will be processed by the _`configfiles/configs/config_bungeni_membership.xml`_ pipeline, similary, parliamentary documents such as
  * `question`, `bill` e.t.c will be passed to _`configfiles/configs/config_bungeni_parliamentaryitem.xml`_; And so forth.

To get the correct name of the document-type you would like to add. Bungeni specifies a `@name` attribute in the root node of every serialized document. In the case below, it is a _`committee_member`_ type of document.
```
<contenttype name="committee_member">
   ...
</contenttype>
```
Use this value with the corresponding pipeline configuration file.

## Step 2 - pipeline configuration

All the pipelines configuration files are located here:
```
{BUNGENI_APPS}/glue/resources/configfiles/configs/
```
So, if your new document is a legislative document, open `configfiles/configs/config_bungeni_parliamentaryitem.xml` and add a node to the existing map nodes.
```
...
<parameter name="type-mappings">
    <value>
        <map from="question" uri-name="Question" element-name="question" />
        <map from="bill" uri-name="Bill" element-name="bill" />
        <map from="agenda_item" uri-name="AgendaItem" element-name="agendaItem" />
        <map from="motion" uri-name="Motion" element-name="motion"  />
        <map from="tabled_document" uri-name="TabledDocument" element-name="tabledDocument"  />
        <map from="event" uri-name="Event" element-name="event" />
    </value>
</parameter>
...
```

### map node attributes

The type-mappings allows decoupling with the name used in Bungeni and may affect the underlying framework used to render documents in eXist-db (see [Bungeni XQuery Framework](http://code.google.com/p/bungeni-exist/wiki/BungeniXQFramework)).
The map node has three attributes.

  * map/@from - the pipeline name as set in _`glue.ini.tmpl`_
  * map/@uri - A constant ontology name used as part of the document URI
  * map/@element-name - is the identifier for this document type


You will need to run:
```
./fl config_glue
```
in order to re-generate an updated configuration file from `glue.ini.tmpl`. Restart the `exist-sync` service on Supervisor or on the terminal by running the command below:
```
./fl stop_service:exist-sync start_service:exist-sync
```