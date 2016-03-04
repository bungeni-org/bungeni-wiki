# Introduction

BUNGENI's Parliamentary Registry is a strategic feature of Bungeni. This is an attempt to clarify that what BUNGENI's Parliamentary Registry is meant do do and what data it should store.

# Details

One of the main requirements of a Parliament is to be able to track precisely the status of activities.

The bill process is the one that better exemplifies this need and its requirements. Parliaments have the need to register events about bills and amendments such as presentation, assignment in committees, procedure of the committee, etc. Note that the actual content of the bill (which is an XML document) is not stored in the registry: only the information about the states and events that cause these states are stored. The link between the Bill information in the registry and the actual Bill document is done via a common naming / identifier scheme, described below.

To achieve this objective, it is also necessary to acquire data about MPs, Parliamentary Groups composition, info about political parties, Committee composition, Government composition, etc. Apart from capturing current information about an entity, it is also necessary to keep track of how these entities evolve over time; for instance, a Committee composition can change during the lifetime of a parliament -- members can leave or join, the chairman of a committee can change, etc. It is necessary to keep track of such historical information.

Entities in the parliamentary registry are identified using a **standard naming convention**.  For document-like entities -- like Bills, DebateRecords and Hansards a URI naming convention is used that uniquely identifies every document within the system. (Refer to [AkomaNtoso standard, URI naming convention](http://www.akomantoso.org/release-notes/akoma-ntoso-1.0-schema/naming-conventions-1)).   The same URI convention is used by the URI resolver that resolves every parliamentary document URI to a physical document.

For non-document-like entities like Members of Parliament, Committees, Parliamentary groups, a similar naming convention is used (Refer to [AkomaNtoso standard, naming convention for non-document entities](http://www.akomantoso.org/release-notes/akoma-ntoso-1.0-schema/naming-conventions-1/uri-of-non-document-entities)).

The identifier naming scheming is used for maintaining referential integrity across parliametary data. The naming scheme is also used in the context of representing parliamentary data in the form of AkomaNtoso XML documents, and is used by an XML indexer for search / retrieval purposes. For example:

  * The member of parliament is referenced within the DebateRecord, the XML document of the debate record is marked up to identify members of parliament using the naming scheme.
  * The Bill document is identified uniquely using a URI. This URI is also used for resolving the URI to a physical bill document.

The Parliamentary Registry, in order to record information about the life-cycle of parliamentary activities and support the creation referential links stores data about:
  * Documents (bills, questions, motions, tabled documents, reports, etc. lifecyle - data and procedural events about presentation, notifications, stages, votes, etc. of documents in the plenary and committees)
  * MP and Staff (personal data and roles)
  * Parliament, Committees, Political Groups, Governments, Ministries etc (nominations, composition, roles, portfolio, etc.)
  * Calendar / Scheduled Events (Session opening date, adjournment date, resumption date, etc. agenda items, events and occurrences). Apart from that, Calendard events are also generated during the lifecycle of various parliamentary documents. For example, during the bills proces a vote could be scheduled to vote for the bill - this also is a calendar event, or the scheduling of a question for discussion on the floor of parliament would also be a calendard event.
  * Place (Constituencies, geographical locations)
  * Classification (Thesaurus, controlled vocabulary)
  * Data about official publications e.g. the acts, decrees, international treaties, etc.

Registry data are never overwritten or deleted.  Registry data are made available to other applications both withing the Bungeni system and also to external applications.

# Parliamentary Registries: component architecture

The Parliamentary Registry is constructed via serialization of content within the portal with additional change metadata to a relational database. It operates via synchronous synchronization of modifications to the Bungeni Portal, to a relational database, via subscribing to object events broadcast by the Portal. The portal out of the box already provides events for objects being added, modified, or deleted. Additional modifications to the portal for an initial implementation have also enabled events for workflow. The major benefit of this mechanism is that the Registry implementation is kept out of band of the codebase, and its domain concern is not interleaved with the portal implementation.

The Event subscribers utilize the extant 3rd party python packages of [sqlalchemy](http://www.sqlalchemy.org/), [ore.alchemist](http://pypi.python.org/pypi/ore.alchemist/0.1.0), and [ore.auditlog](https://svn.objectrealms.net/view/public/browser/ore.auditlog/trunk) to provide the core functionality. The BungeniRegistry product extends and configure these libraries with functionality specific to the registry. Sqlalchemy provides database abstractions for connection handling, and sql dialect abstractions, as well as an object relational mapping tool. ore.alchemist extends this with zope transaction integration and support for zope3 schemas and archetypes schemas. the ore.auditlog provides a base implementation of content serialization to a database in response to events. BungeniRegistry provides configuration of connection handling, event subscriber configuration, and versioning handling.

One of the premminent concerns when discussing RDBMS driven systems is the schema design. It's important to emphasize that the schema in the registry is dynamic. It aims to effect a complete capture of information recorded in the portal. Normal mechanisms of mapping configuration (static) would cause a hindrance to development, with the need to adjust the schema in response to changes within the portal. Instead, the database schema will be dynamically constructed via dynamic transformation of the archetypes schema, and injected with version classifiers, with tables on a per content type basis. The unique primary key for a table is the archetypes UID and version id.

The introduction of a version id introduces complications regarding mapping archetype references between objects as these references are not version aware. Operationally this shouldn't be an issue as long as the target relational database supports multicolumn foreign keys (Postgres does). A further restriction for the application model is that the archetypes relation needs to be constrained using the [Relation](http://plone.org/products/relations) library. The reason for this is that standard archetypes references are fairly free-form, and can exist between any two objects in the system regardless of type. These references can be of any cardinality and are bidirectional. A mapping of type to relational database needs additional qualifiers to properly express the relation. The relations library provides the specification of content type endpoints and cardinality of a relation.

For groups, TeamSpace membership in this would just work, since membership is  just another content type stored in the RDBMS. An additional field will need to be injected to capture the containment hierarchy as query foriegn key, so all members of a group can be queried.

One current failing of this modeling is that cross-type queries, i.e. queries like "find all the latest changes to the system" are problematic if version change information is captured solely in types tables. An optional way to try and capture this would be to model change type information into its own table, but storing all changes in the entire portal in a single table might introduce performance issues for the table over the lifetime of the database, unless periodic archives can be performed, or queries for the table are done in a separate OLAP database.

Another question is regarding the storage of file/binary content in the registry... namely if it should be done. Initial discussions were that it shouldn't be.



