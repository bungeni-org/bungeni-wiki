# Introduction

In the Bungeni zope3 system a lot of the boiler plate code is generated at runtime from the python representation of the data model, which can be interacted with outside of Zope.

The data model is defined in `bungeni.main/trunk/bungeni/models/schema.py`
The domain model is defined in `bungeni.main/trunk/bungeni/models/domain.py`
The domain model is mapped to the database schema by the orm model in `bungeni.main/trunk/bungeni/models/orm.py`

This provides a functional API which can be interacted from a python interpreter. An example of this is provided in the doc test.

`bungeni.main/trunk/bungeni/core/readme.txt`

The doctests can be run via

`./bin/test -s bungeni.core`


# Auto generation using catalyst

The user interface and the portal builds on top of this core data model.
Runtime auto-generation facilities (alchemist.catalyst) are used to create much of the system.  To achieve this auto generation the core data model is annotated with additional metadata (e.g. what sort of widgets to use in forms what kind of fields in a listing, which permissions to use on a field, i18n labels , form help).
These metadata components called 'model descriptors' are similar to archetype schemas or django models. The model descriptors are defined in `bungeni.main/trunk/bungeni/ui/descriptor.py`

Additonal behaviors like versioning, relation ui, workflow security, audit are also driven by the same model descriptors.

e.g. QuestionDescriptor :

```
class QuestionDescriptor(ParliamentaryItemDescriptor):
    display_name = _(u"Question")
    container_name = _(u"Questions")
    custom_validators = ()
    
    fields = deepcopy(ParliamentaryItemDescriptor.fields)
    fields.extend([
        Field(name="question_id", modes=""),
        Field(name="question_number",
            modes="edit|view|listing",
            property=schema.Int(title=_(u"Question Number"), required=False),
        ),
        Field(name="supplement_parent_id",
            label=_(u"Initial/supplementary question"),
            modes="",
            view_widget=SupplementaryQuestionDisplay,
        ),
        Field(name="ministry_id",
            modes="view|edit|add|listing",
            property=schema.Choice(title=_(u"Ministry"),
                source=vocabulary.MinistrySource("ministry_id"),
            ),
            listing_column=ministry_column("ministry_id" , _(u"Ministry")),
        ),
        Field(name="approval_date",
            modes="edit|view",
            property=schema.Date(title=_(u"Date approved"), required=False),
            edit_widget=DateWidget,
            add_widget=DateWidget,
        ),
        Field(name="ministry_submit_date",
            modes="edit|view",
            property=schema.Date(title=_(u"Submitted to ministry"), 
                required=False
            ),
            edit_widget=DateWidget,
            add_widget=DateWidget,
        ),
	    ...
    ])
    public_wfstates = get_states("question", tagged=["public"])

class QuestionVersionDescriptor(VersionDescriptor):
    display_name = _(u"Question version")
    container_name = _(u"Versions")
    fields = deepcopy(VersionDescriptor.fields)

```


There are many examples of descriptors in the descriptor model -- it is essentially a sub-class with a list of bungeni.alchemist.model.Field descriptor instances. Based on the schema for the domain object in question there is an appropriate field descriptor. The order of the fields in the descriptor defines the order of appearance on the form. If no field is specified explicitly for listing model, all fields are used.

To know what can be set on a descriptor -- refer to bungeni.alchemist.model.Field for reference information about all things that can be set on a Field descriptor.

Here is an extract of bungeni.alchemist.model.Field:
```
class Field(object):
    interface.implements(IModelDescriptorField)
    
    # INIT Parameter (and Defaults)
    
    # CONVENTION: the default value for each init parameter is the value 
    # assigned to the corresponding class attribute - reasons for this are:
    # a) makes it easier to override init param default values by trivial 
    # subclassing.
    # b) no need to set an instance attribute for init parameters that are 
    # not set, simply let the lookup pick the (default) value off the class.
    
    name = None      # str : field name
    label = ""       # str: title for field
    description = "" # str : description for field
    
    modes = "view|edit|add" # str : see _valid_modes for allows values
    
    property = None # zope.schema.interfaces.IField
    
    listing_column = None   # zc.table.interfaces.IColumn
    
    view_widget = None      # zope.app.form.interaces.IDisplayWidget
    edit_widget = None      # zope.app.form.interfaces.IInputWidget
    add_widget = None       # zope.app.form.interfaces.IInputWidget
    search_widget = None    # zope.app.form.interfaces.IInputWidget

    _valid_modes = ("view", "edit", "add", "listing", "search")    
```

The file `ui/catalyst.zcml` specifies the declaration which explicitly applies the generation components to the domain model using the descriptor definition.
An optional debugging attribute is provided here -- setting `echo="True"`  on the db:catalyst declaration will log to standard output all catalyst.zcml processing.

e.g. catalyst.zcml entry for Question :

```
    <db:catalyst class="bungeni.models.domain.Question"
        descriptor=".descriptor.QuestionDescriptor"
        interface_module="bungeni.models.interfaces"
        ui_module="bungeni.ui.content" 
        echo="False"
    />
```

# Putting it all together

At this point you would have the forms, container, interface
views of the content type from the generation but isn't yet wired into the site
layout/hierarchy.

To do that look at  `bungeni.main/trunk/bungeni/core/app.py` the app object is basically the root of the site, this includes an `appsetup` adapter which setups the application structure when the app server is started. you can adjust this to add a container for a new content type.

Parliamentary content types get auditing, indexing, and versioning behavior and ui applied,  via marker interfaces on the domain class, which triggers event subscribers, views, and adapters to integrate those features with the content classes.


# Permission Setting

Permission checkers are constructed from class zcml directives, in `models/domain.zcml`, which allow for permissions definition for read / edit access for a set of fields or interfaces. This is done by the catalyst process.

**DEVELOPER NOTE** -
Field level permissions and view level filtering of fields is done via alchemist.catalyst.domain (which sets up the checkers according to the model descriptor settings -- see the ApplySecurity API) and alchemist.ui.core for filtering fields based on permissions. But, presently field level view\_permission and edit\_permission are deprecated, as they are always overriden by what is declared for the corresponding attribute in domain.zcml. However, alchemist.catalyst.model.ApplySecurity API needs to have these attributes available on Field descriptor instances.

In general we try to minimize the number of distinct permissions we have whenever possible, as we need to roundtrip to the db for each distinct permission checked (a permission decision is cached on the proxy for the lifetime of the request).
