# Design: Declarative Bungeni

A design review of how the Bungeni core system defines and manages its business object types, in light of making it as organization-agnostic as possible with an intention to maximize its applicablity to a wider variety of "parliament-like" institutions.

The underlying conceptual criteria driving this redesign are:

  1. the core system should have support for:
    1. a selected set of "archetypes", each embodying a formal re-usable model of a parliamentary domain concept, e.g. document and its core features e.g. title, status, origin, destination, output, and group and its core features e.g. status, membership;
    1. a set of plug'n'play features that are commonly--but not necessarily always--needed by domain object types of parliament-like institutions e.g. documents are typically workflowed, are sometimes versioned, may sometimes need to support attachments or specific groups may sometimes hold sittings. Such "features" are defined vertically, all-inclusive of the persistance+business+ui layers, and may be enabled at will for a given parliamentary archetype incantation.

  1. the core system should not impose the kind and number of business object types but allow each Bungeni deployment instance the freedom to define these--without requiring any change in the core Bungeni code base (again, throughout persistance+business+ui layers). Thus, a custom parliamentary object type is defined as an incantation of an archetype supported by the core system, where each such configured archetype declares:
    1. which of the archetype's (optional) core properties to enable (ui.xml);
    1. any additional custom properties, and their type (from a predefined list of supported types);
    1. type-level settings;
    1. workflow;
    1. which extensible features are to enabled.


## Overview

A Bungeni deployment will consist of any number of customized archetypes, along with the core support types and core handling of any added custom attribute.

The relational db schema will be substantially simplified, having less tables, while at the same time becoming more stable, no longer needing to be changed to add a custom document type or an additional custom property to a document; it will change to consist of:

  * a single fixed table per archetype;
  * a single change table per (applicable) archetype (currently 8);
  * a single version table per (applicable) archetype (currently 6);
  * vocabularies should be moved out of the db (12 tables) and into configuration, in some unified way !+ a couple of these may not fit a common definition;

Other tables may be required, depending on how extended attributes are implemented: if horizontal, simply add, on each archetype, a couple custom columns per foreseen datatype; if vertical, there would need to be a single extension table for **each** supported custom **attribute datatype** (per archetype, archetype\_change, archetype\_version?);

Remarks:
  * !+personal\_annotations: personal annotations on any object in the system, only for own use, may be stored independently, to not clutter the main bungeni db needlessly.
  * !+dc\_adapters: need to also express these for archetypes.


## Core Support

See the current vertical implementation (partial, as not inclusive of the UI) of the auditable and versionable features on the document archetype as a "pattern to refine" for implementing all vertical features.

A notable difference in this scenario would be that there will be no need to dynamically create db tables (the db schema should not need to be dynamically manipulated), so will actually be simpler; there would however still be the need to dynamically modify the domain class model and the mapping of that to the schema, as per the deployment's customization.

Legend for abbreviations used below:
```
    Archetypes:
        D = Document
        E = Event(Document)
        R = Report(Document)
        G = Group
        M = Membership
        U = User
    Aspects:
        OA = (Core) Optional Attributes
        EA = Extended Attributes 
        TS = Type-level settings
        WF = Workflow
        EF = Extended PNP Features
    Qualifiers:
        -?: the item exists but maybe it shouldn't
        +?: the item does not exist but maybe it should
```

### Concrete Core Types

The following concrete types (not including the archetypes to be used to define custom types) are defined by the core system as they embody available "features". Core optional attributes (ui.xml), extension attributes (ui.xml?) and workflow (for workflowed concrete types) are **always** customizable on all concrete types, as indicated below.
```
------------    ------------------------    ------------
Feature:        Applicable for:             Customizable in:
------------    ------------------------    ------------
Change:         D   E+? R+? G+? M+? U+?     OA  EA
Version:        D   E+? R+? G+? M+? U+?     OA  EA
Attachment:     D   E+? R+? G+? M+? U+?     OA  EA
Event:          D       R+? G+? M+? U+?     OA  EA
Address:                    G   M+? U       OA  EA  WF-?
Signatory:      D               M+?         OA  EA  WF
Sitting:                    G               OA  EA  WF
Heading:                                    OA  EA  WF-?
AgendaItem:                                 OA  EA  WF
Reference+?:    D                           OA  EA  
Notification+?: D                           OA  EA  WF+?
------------    ------------------------    ------------
```

Remarks:

  * the feature is stated as a singular **thing** but in all cases it implies support for **multiple** of each **thing**.
  * Versionable must have auditable (but not vice-versa); there may be other feature inter-dependencies (but these should be kept to a minimum).
  * !+sitting, session, calendar, minutes, heading, agendaitem: are these the same "feature" i.e. they always exist together, whenever there is one there are the others?
  * !+versionability of some sub-items: some (namely, attachments) are versionable in and of themselves, as well as being snapshot-able/point-in-time-able along with the parent item when that is being versioned: versionable and point-in-time-able are distinct aspects... the first being a pnp feature on attachments, the second being an inherent characteristic of attachments (as a concrete support type).
  * !+references: is should this be a PNP feature? is this a kind of "attachment"?
  * !+address: should memberships have the ability to have addresses? (Similar to the previous Title address.)


### Event

`Event` is both a concrete type (representing a document feature) as well
as an achetype in and of itself as users are able to define special events,
with specific attributes and workflow.

Expected usage of specialized events are as "outcome documents"
e.g. a motion's resolution or a question's response, as well as for the
concept of multiple "group assignment" that does not necessarily affect the
parent document's workflow.

Here's a shortlist of foreseen sample custom "incantations" of events,
as "customized" features on Document:

```
------------        ------------
Event:              Incantations are customizable in:
------------        ------------
MotionResolution    OA    EA    TS    WF    EF
QuestionResponse    OA    EA    TS    WF    EF
AssignedGroup:      OA    EA    TS    WF    EF
------------        ----------------
```


## Archetypes

We identify the archetypes (as elaborated in the conceptual criteria above): Document, Event, Report, Group, Membership, User. Archetype incantations are customizable in all the ways concrete types are customizable, plus additionally in "Type-level settings", in "Extended PNP Features":
```
------------    ------------
Archetype:      Incantations are customizable in:
------------    ------------
Document:       OA    EA    TS    WF    EF
    Event:      OA    EA    TS    WF    EF
    Report:     OA    EA    TS    WF    EF
Group:          OA    EA    TS    WF    EF
Membership:     OA    EA    TS    WF    EF
User:           OA    EA    TS    WF    EF
------------    ------------
```

Remarks:
  * !+core properties: each archetype elaboration below contains such sub-section...  until each is filled in, assume that all the current "core" features of each archetype will be included here e.g.
  * a document supports possibility to be "minuted" (for if/when it is discussed in a sitting)
  * a group can have members and has a role associated with it, etc.
  * !+root\_container: the meaning of root\_container is archetype dependent (polymorphism)... for Document, this means same as what is meant by the current "parliament\_id", for Membership it would mean something else, namely that for the user to be a member of this group he must also be a member of the root\_container group. This would be similar in logic to declaring for example different archetypes to be "Versionable", as for Document this would imply a certain handling (as done currently) while for Group it could imply a very different handling (not done currently).


### DOCUMENT

```
core properties: 
    ...

type-level settings:
    root_container:GROUP = None
        "inside" (owned by) a specific group [e.g. parliament] or not?
    outcome_document_type:DOCUMENT_TYPE = None
        states the type of the outcome document, if any
        must be None if this is already an outcome document type
    
features: <workflow
    ...
    auditable="true" # changes
    versionable="true" # versions, implies auditable
    ...
    Attachments
    Signatories
    Events
    Annotation
    
    References+? # to related docs: other Bungeni documents (a uri?), 
      # a digital doc, an external source, an arbitrary text reference 
      # !+ is this an explicit list associated list of refs or are these 
      # inlined as part of text content?
    Notifications # to who, triggered by a workflow transition or time, 
      # or combined; reminders of mtgs
>
```

All Documents are potentially schedulable (a core Document feature) for a Group's sitting--as long as their workflow definition meets the sub-workflow requirements of the Group's sitting. As an implementation possibility of how to declare when a document is in a "schedulable" state, a similar idea to the old tagged mechanism (was being used to conveniently group logical sets of workflow states) may be used i.e. add a (predefined) tag "schedulable" on the relevant states, by which the calendaring could determine what documents are available for scheduling and, moving on, from this state the workflow should define a transition to another state tagged with something like "scheduled".

An independent concept to this is the "scheduling" of a document for discussion in a sitting that is independent of the document's workflow. This should be simply left entirely to the Group wishing to include a document for discussion in this way--any and all documents (and in any workflow state) are available for such scheduling.

Remarks:

!+itemize: to reduce any ongoing confusion such a distinct term should probably be used for this.

!+Notifications should probably be handled separately, and having more than one layer:
  * a "core" layer that defines a subscription declaration mechanism (which source objects and under what conditions) for all internal notifications, that are to be defined by the site's admin (customization at workflow/type level).
  * an outer layer that lets each user define for own self how those notifications are to be dispatched, as sms, email, etc.
  * In addition there should a few "general" user-options, that can be used to personalize the behaviour a little more e.g. "always-send-me-meeting-reminders: 24 hours before, 2 hours before".
  * A further feature could be to allow anonymous users to subscribe (via email) to general events e.g. a new parliamentary document, as well as to track a specific document.


#### Sample Custom Document Types

```
Bill
Motion
Question
TabledDocument
```


### EVENT

Events are a special kind of Document (but not a parliamentary document in the
procedural sense), and inherit most of the features and
behavior from DOCUMENT, with the notable differences that Events have no
`outcome_document_type` (they may be themselves the outcome document), and
do not themselves support the Event feature (cannot have events on an event).

#### Sample Custom Event Types

```
Event (a base concrete Event type)
MotionResolution
QuestionResponse
AssignGroup
```


### REPORT

Reports are a special kind of Document and a parliamentary document in the
procedural sense, and inherit most of the features and
behavior from DOCUMENT, with the notable differences that Events have no
`outcome_document_type` (they may be themselves the outcome document).

#### Sample Custom Report Types

```
Report (a base concrete Report type)
SittingReport
```


### GROUP
```
core properties: 
    ...

type-level settings:
    root_container:GROUP = None
        "inside" (owned by) a specific group [e.g. parliament] or not?
    active_concurrent:bool = True
        only zero or one of these may be active at any time?
    allow_children:bool = True
        does this type of group allow allow having sub-groups?
    membership_type:MEMBERSHIP = Membership
        the membership type to be used to instantiate members
    
features: <workflow
    ...
    Sitting
    Address
>
```

#### Sample Custom Group Types
```
Committee
Parliament
Office
```

### MEMBERSHIP
```
core properties: 
    ...

type-level settings:
    root_container:GROUP = None
        user must be "inside" (member of) the specific group [e.g. parliament]?

features: <workflow ... >
```

The conceptual workflow inherent to memberships is exposed to customization and accommodated via an explicit workflow. An additional aspect of why MEMBERSHIP is an archetype is that memberships have different characteristics, as dictated by their context e.g. an office membership may be for anyone, and not tied to a parliament, but a committee membership may be limited to a member of parliament as well as tied to the parliament (while the "conceptual" committee itself may not be bound to the parliament, but all its membership is!).

#### Sample Custom Membership Types

Membership (a base concrete membership type)


### USER
```
core properties: 
    ...
    
type-level settings:
    <none> or... this space left intentionally blank!

features: <workflow
    ...
    
    Address
    Attachment? !+may simply be an extended binary field?
    Version?
>
```

#### Sample Custom User Types

User (a base concrete User type)



## Extended Attributes

Custom types may declare additional attributes for custom use; such attributes will need to be assigned one of the datatypes that the system will support and expose:

```
text
date
number
binary
vocabulary
```

In addition to adding types, a custom type may disable core **optional** attributes.

How extended attributes are declared, as well as how core optional attributes disabled, will probably be integrated into ui.xml... to be explored.

### implementation note: horizontal or vertical

As mentioned above, extended attributes may be implemented **horizontally** or **vertically**; each approach is a tradeoff of priorities, with implications:

```
horizontal: 
    simplicity + probably faster runtime  VS limited + storage space inefficient
    
vertical: 
    less simple + probably slower runtime VS unlimited + storage space efficient 
```

Horizontal is more familiar, being a normal table definition with hard-wired custom column placeholders. An approach to a vertical implementation could be to have "vertical\_property" tables... for some explanation of the technique see the "examples/vertical" folder at: http://hg.sqlalchemy.org/sqlalchemy/file/

An additional option may be to proceed with a (conservatively applied) horizontal approach and then switch to a vertical approach at a later time as this should not affect configuration declarations in any way.


## User Interface

Declaration of:
  * what menus should exist and what should they point to, and for whom
  * what views should exist and for whom
would have to become part of the forms ui.xml and/or the workflow definitions.

Need a syntax for incorporating this information in those customization files... to be explored.


## Tasking options

This refactoring is elaborate and any opportunities for clear isolatable sub-tasks should be taken. Some sub-tasks that come to mind, that may be started on at any time:

  * design refactoring of event type as a "parliamentary item sub-archetype"
  * remove heading as parliamnentary item
  * rename db schema tables from plural to singular

  * merge down to a single db table for documents/events, groups, memberships (this needs migration of auditing/versioning/attachments from working the various current tables to working with the one table per type).

  * review of how auditing/versioning/attachments are implemented, in particular their inter-dependencies, and the persistence pattern employed (see !+ATTACHED\_FILE\_VERSIONS in src relating to this). This refactoring may still be possible without first doing this though (to be seen on closer examination).

  * merge parliamentary documents into single document archetype infrastructure, and remove all additional python descriptors, and for refactor each extended document feature to be plug'n'play-able.

  * merge parliamentary groups into single group archetype infrastructure, and remove all additional python descriptors, and for refactor each extended group feature to be plug'n'play-able.

  * preparation to facilitate enabling and disabling of features, bind each feature to a (single) dedicated interface, on which to generically register any necessary components.

  * preparation to facilitate enabling and disabling of features, make all component registration to be done programmatically (not via ZCML). This includes all declarations for **views** (pages, viewlets) and **menu items**.

  * "open-up" the current more or less black box handling of auto-generation of UI forms (alchemist) to be able to modify it as necessary to support this refactoring.


**vocabularies**

  * move vocabulary linkages to fk tables to literal string values `[done]` (but following "extended" vocabs may still need to be refactored: venues, countries, title\_types, group\_sitting\_types)
  * migrate all user-owned vocabs to bungeni\_custom xml
  * provide support for proper and manageable translation of vocabulary terms, both for objects that are translated and ones that are not

  * !+vocabularies: currently some (non-derived i.e. enumerations of explicit values) vocabularies are based in the db, some not. Vocabulary sources should be re-organized, into "system-owned" and "user-owned" enumerations; user-owned enumerations should be part of the customization. There should be a generic processor of these enumerations that automatically produces all vocabularies for use in the UI.
  * !+vocabularies: add a description property to all of them (as in committee\_types).
  * !+committee\_types: life\_span, committee\_type\_status extra columns should NOT be on this vocabulary, but should be a property on the committee type itself!
  * !+group\_sitting\_types, title\_types: need special attention... have other columns.


### other tasks, done

  * debundle the "attachments" feature out of "versionable" `[done]`
  * make membership workflowed `[done]`
  * define UI / Workflow XML dialext extensions to support additional features here `[done]`

