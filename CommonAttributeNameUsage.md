# Introduction

The aim of this document is to describe common attribute names in groups and parliamentary items. Presently in the default setup of the system - there is a lot of confusion and misuse of attributes. This document will propose a standardized naming scheme across both types and describe the use of the attributes.

This is applicable to both the current system (where individual parliamentary item types and groups share only a limited set of the model ) and the future in terms of declarative Bungeni. The motivation to fixing it in the current is to make migration to the declarative system easier.

# Incorrect Usage

## Groups

short\_name is presented as "Acronym"

```
        Field(name="short_name", # [user-req]
            modes="view edit add listing",
            localizable=[
                show("view edit"),
                hide("listing"),
            ],
            property=schema.TextLine(title=_("Acronym")),
            #listing_column=name_column("short_name", _("Name"))
        ),

```

full\_name is presented as "Name"

```

Field(name="full_name", # [user-req]
            modes="view edit add listing",
            localizable=[
                show("view edit"),
                hide("listing"),
            ],
            property=schema.TextLine(title=_("Name")),
            #listing_column=name_column("full_name", _("Full Name"))
        ),
```


Parliaments (which is derived from Group)

short\_name is used as "Parliament Identifier"

```

Field(name="short_name", # [user-req]
            modes="view edit add listing",
            localizable=[
                show("view edit listing"),
            ],
            property=schema.TextLine(title=_("Parliament Identifier"),
                description=_("Unique identifier of each Parliament "
                    "(e.g. IX Parliament)"),
            ),
        ),
```

full\_name is presented as "Name"

```
fields = [
        Field(name="full_name", # [user-req]
            description=_("Parliament name"),
            modes="view edit add listing",
            localizable=[
                show("view edit listing"),
            ],
            property=schema.TextLine(title=_("Name")),
            listing_column=name_column("full_name", "Name"),
        ),

```

Government (which is derived from Group)

short\_name is used as "Name"

```
  Field(name="short_name", # [user]
            modes="view edit add listing",
            localizable=[
                show("view edit add listing"),
            ],
            property=schema.TextLine(title=_("Name"),
                description=_("Name"),
                required=False
            ),
```

full\_name is used as "Number"

```
Field(name="full_name", label=_("Number"), # [user]
            modes="view edit add listing",
            localizable=[
                show("view edit add listing"),
            ],
        ),
```


## Parliamentary Items

short\_name is presented as Title :

```
  Field(name="short_name", # [user-req]
            modes="view edit add listing",
            localizable=[
                show("view edit listing"),
            ],
            property=schema.TextLine(title=_("Title")),
            edit_widget=widgets.TextWidget,
            add_widget=widgets.TextWidget,
        ),
```

full\_name is presented as Summary :

```
        Field(name="full_name", # [user]
            modes="view edit add",
            localizable=[
                show("view edit add"),
            ],
            property=schema.TextLine(title=_("Summary"), required=False),
            edit_widget=widgets.LongTextWidget,
            add_widget=widgets.LongTextWidget,
        ),
```

# Common Attribute Names

Groups and Parliamentary Items will have a different model - however there needs to be a standardized naming convention use for attribute names.

The following is a preliminary list of common standardized attribute names for both types - the name specified in the first column should be the name used both in groups and parliamentary items.

|common attribute|current use in PI| current use in GRP| description |
|:---------------|:----------------|:------------------|:------------|
|short\_title    |short\_name RENAME| short\_name RENAME| a short title |
|long\_title     |full\_name RENAME| full\_name RENAME | a long title |
|acronym         |NOT PRESENT      |NOT PRESENT(misused as short\_name) | an acronym for the item or group |
|item\_number    |bill\_number, motion\_number etc RENAME | NOT PRESENT (parliament identifier misued as short\_name) | a human entered identifier for the group / human or automaticlally generated number for parliamentary item  |
|sort\_order     | NOT PRESENT / NOT REQUIRED | NOT PRESENT / TO BE ADDED | to specify a custom sort order in a listing |
|description     | description     | description       | Description of group/item |
|type            |type             |type               | type of group, parliamentary item (parliament, committee or motion, question ) |


# Reference Issues

  * [Issue 747 Group Incorrect use of fields on the UI to underlying storage](http://code.google.com/p/bungeni-portal/issues/detail?id=747)
  * [Issue 755 Parliamentary Item  Additional Metadata Attributes](http://code.google.com/p/bungeni-portal/issues/detail?id=755)
  * [Issue 754 Group : Additional Metadata Required ](http://code.google.com/p/bungeni-portal/issues/detail?id=754)



