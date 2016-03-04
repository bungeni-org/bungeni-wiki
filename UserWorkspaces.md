

# Introduction

This document contains a description of workspaces and a proposed scheme of implementing them.
A workspace is a section of Bungeni where authenticated users see the progress of their parliamentary documents.

# Problem Description

Currently workspaces are hard-wired to user roles. There are IXXXWOrkspace interfaces mapped to specific roles in the system, and then documents are mapped to the the workspace tabs via the tag->state mapping mechanism.

This is clumsy because when you add a new role, it requires adding all the IXXXWorskpace interface machinery into the code and also requires setting all the mappings in python code.

A workspace system that does not require editing source code whenever a new role is required to be added is neccessary.

# Proposed Solution

Every logged in user shall get a workspace with 5 tabs
  1. drafts - items that the user is currently working on
  1. inbox - items that require the users immediate attention
  1. sent - items that the user previously worked on and that no longer require the users attention
  1. archive - the documents that the user was directly involved in the workflow for, but which have now reached the end of life-cycle
  1. all documents -- the documents that may be assigned to the user role directly, but the user may not be involved in their workflow.

Every possible document state is covered by the tabs above, any other tabs will be searches.
In addition to this, a user shall be able to create and save custom filters by creating search based tags.

## Main tabs

Ever state in the workflow of every document will need to specify which tab the document should be displayed in that state.

A new tag `<workspace>` will be used for this purpose. For example

```
<state id="first_reading_adjourned" title="First Reading Adjourned" like_state="gazetted">
    <workspace>
          <tab name="inbox" roles="..."/>
          <tab name="sent" roles="bungeni.Clerk bungeni.Speaker"/>
          <tab name="draft" roles="bungeni.Owner"/>
          <tab name="...." roles="..."/>
    </workspace>
</state>
```

In the above example `tab/@name=draft` and `tab/@name=sent` may be left out since they may not apply for that state.

The following rules apply to the use of roles in the workspace definition :

  1. A role may only appear once in the whole workspace definition ie. the same document  cannot appear in more than one tab.
  1. An exception shall be raised if this happens.
  1. the `@like_state` attribute inherits only `grant` and `deny`, not workspace definitions
  1. if a role is denied permission to view a document while being specified in a tab for the document to appear in, An exception shall be raised -e.g.:
```
    <state ...>
       <workspace>
         ...
         <tab name="inbox" roles="bungeni.Clerk" />
       </workspace>
    
       <deny permission="zope.View" role="bungeni.Clerk" />
    </state>
```

Notes
  * The listings will check that the user has zope.View permission on an item
  * where are the available tabs themselves defined in the system ?

## Multiple Workspaces

Some users like ministers may have both the MP role and the Minister role. In the current way - the system presents 2 workspaces - however, this is done because the tabs shown to the 2 roles were varying.

With the proposed solution -- only 1 workspace will be shown -- with the documents in the tabs appearing on the basis of them satisfying the rules for  **either** role.

## Filters

Users will have the oppurtunity to add new tabs that will essentially be saved xapian searches.
TODO : EXPAND

## Alternate Syntax

Alternative syntax suggestions proposed were to define the workspace within the workflow root element rather than individually within every state.
Same rules as described above would apply.
```
<workflow....>
<workspace role="bungeni.Clerk" >
  <tab name="draft" states="draft working_draft ..." />
  <tab name="inbox" states="recieved require_response ..." />
   .....
</workspace>

<workspace role="bungeni.MP" >
   ...
</workspace>

<state id="draft" ...>
</state>
...

<transition ...>

</transition>
```