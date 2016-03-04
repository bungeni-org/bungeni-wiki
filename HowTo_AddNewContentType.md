

## Create a new content type

To add a new content type you need to set up DB tables , classes and mappers. All these classes go in bungeni.models packages:

  * in schema.py you define the underlying DB tables. For example we follow the Bill content type:

```

bills = rdb.Table("bills", metadata,
    rdb.Column("bill_id", rdb.Integer,
        rdb.ForeignKey("parliamentary_items.parliamentary_item_id"),
        primary_key=True
    ),
    rdb.Column("bill_type_id", rdb.Integer,
        rdb.ForeignKey("bill_types.bill_type_id"),
        nullable=False
    ),
    rdb.Column("ministry_id", rdb.Integer, rdb.ForeignKey("groups.group_id")),
    rdb.Column("identifier", rdb.Integer),
    rdb.Column("publication_date", rdb.Date),
)
bill_changes = make_changes_table(bills, metadata)
bill_versions = make_versions_table(bills, metadata, parliamentary_items)

```

This code generate the Bill table and the associated table for versioning and to log changes.

  * in domain.py the classes are defined:

```

class Bill(ParliamentaryItem):
    cosignatory = one2many("cosignatory", 
        "bungeni.models.domain.CosignatoryContainer", "item_id")
    event = one2many("event", 
        "bungeni.models.domain.EventItemContainer", "item_id")
    assignedgroups = one2many("assignedgroups",
        "bungeni.models.domain.GroupGroupItemAssignmentContainer", "item_id")
    versions = one2many("versions",
        "bungeni.models.domain.BillVersionContainer", "content_id")
    sort_on = ParliamentaryItem.sort_on + ["submission_date"]

BillChange = ItemLog.makeLogFactory("BillChange")
BillVersion = ItemVersions.makeVersionFactory("BillVersion")

```

The last two are factory methods to generate the classes from Bill.

  * in orm.py the classes are mapped to the DB tables:

```

mapper(domain.Bill, schema.bills,
    inherits=domain.ParliamentaryItem,
    polymorphic_on=schema.parliamentary_items.c.type,
    polymorphic_identity="bill",
    properties={
        "changes": relation(domain.BillChange, 
            backref="origin",
            cascade="all, delete-orphan", 
            passive_deletes=False
        )
    }
)
mapper(domain.BillChange, schema.bill_changes)
mapper(domain.BillVersion, schema.bill_versions,
    properties={
        "change": relation(domain.BillChange, uselist=False),
        "head": relation(domain.Bill, uselist=False),
        "attached_files": relation(domain.AttachedFileVersion,
            primaryjoin=rdb.and_(
                schema.bill_versions.c.content_id ==
                    schema.attached_file_versions.c.item_id,
                schema.bill_versions.c.version_id ==
                    schema.attached_file_versions.c.file_version_id
            ),
            foreign_keys=[
                schema.attached_file_versions.c.item_id,
                schema.attached_file_versions.c.file_version_id
            ]
        ), 
    }
)
```

  * in bungeni.ui.descriptor the zope schema are mapped to the sqlalchemy. In the specific a class derived from ModelDescriptor class can declare for each field configuration parameters (name, label, ...), which widget to use and in some cases (eg. bill\_type\_id) the source of values to show:

```
class BillDescriptor(ParliamentaryItemDescriptor):
    display_name = _(u"Bill")
    container_name = _(u"Bills")
    fields = deepcopy(ParliamentaryItemDescriptor.fields)
    _bt = misc.get_keyed_item(fields, "body_text", key="name") # !+
    _bt.label = _(u"Statement of Purpose")
    _bt.property = schema.Text(
        title=_(u"Statement of Purpose"), required=False)
    
    fields.extend([
        Field(name="bill_id", modes=""),
        Field(name="bill_type_id", 
            property=schema.Choice(title=_(u"Bill Type"),
                source=vocabulary.DatabaseSource(domain.BillType,
                    title_field="bill_type_name",
                    token_field="bill_type_id",
                    value_field="bill_type_id"
                ),
            ),
        ),
        Field(name="ministry_id",
            property=schema.Choice(title=_(u"Ministry"),
                source=vocabulary.MinistrySource("ministry_id"), 
                required=False
            ),
        ),
        Field(name="identifier",
            modes="edit|view",
            property=schema.Text(title=_(u"Identifier"), required=False),
        ),
        Field(name="publication_date",
            modes="view|edit|add|listing",
            property=schema.Date(title=_(u"Publication Date"), required=False),
            edit_widget=DateWidget,
            add_widget=DateWidget ,
            listing_column=day_column("publication_date", 
                _(u"Publication Date")
            ),
        ),
    ])
    public_wfstates = get_states("bill", not_tagged=["private"])

class BillVersionDescriptor(VersionDescriptor):
    display_name = _(u"Bill version")
    container_name = _(u"Versions")
    fields = deepcopy(VersionDescriptor.fields)

```

  * the bungeni.ui catalyst.zcml defines relation between bungeni.models.domain classes and bungeni.ui.descriptor classes:
```

    <db:catalyst class="bungeni.models.domain.Bill"
        descriptor=".descriptor.BillDescriptor"
        interface_module="bungeni.models.interfaces"
        ui_module="bungeni.ui.content"
        echo="False"
    />
    <db:catalyst class="bungeni.models.domain.BillVersion"
        descriptor=".descriptor.BillVersionDescriptor"
        interface_module="bungeni.models.interfaces"
        ui_module="bungeni.ui.content" 
        echo="False"
    />

```

On application startup ore.alchemist package (SQLAlchemy actually) does a lot of things like defining the Interface classes (bungeni.models.interfaces), populates bungeni.ui content.py and creates the containers defined with one2many(...) function in bungeni.models.domain.

For example above in the class Bill, there is the declaration of bungeni.models.domain.BillVersionContainer:

```
    versions = one2many("versions",
        "bungeni.models.domain.BillVersionContainer", "content_id")
```

instead the container for Bill is defined in Parliament class (and in another class):
```
class Parliament(Group):
    ...
    bills = one2many("bills", 
        "bungeni.models.domain.BillContainer", "parliament_id")
```

In general all those **Container are created by ore.alchemist.**

## Add a new form based on a content type

With the definition of the ModelDescriptor derived class most of the setup is already done. The _add_, _edit_, _view_ forms are auto-generated from these classes.

The configuration is defined in bungeni.ui.form configure.zcml, for example:
```
    <browser:page name="view"
        for="bungeni.alchemist.interfaces.IAlchemistContent"
        class=".common.DisplayForm"
        permission="zope.View"
        layer="bungeni.ui.interfaces.IBungeniSkin"
    />
    <browser:page name="add"
        for="bungeni.alchemist.interfaces.IAlchemistContainer"
        class=".common.AddForm"
        permission="zope.ManageContent"
        layer="bungeni.ui.interfaces.IBungeniSkin"
    />
    <browser:page name="edit"
        for="bungeni.alchemist.interfaces.IAlchemistContent"
        class=".common.EditForm"
        permission="zope.ManageContent"
        layer="bungeni.ui.interfaces.IBungeniSkin"
    />
    <browser:page name="delete"
        for="bungeni.alchemist.interfaces.IAlchemistContent"
        class=".common.DeleteForm"
        permission="zope.ManageContent"
        layer="bungeni.ui.interfaces.IBungeniSkin"
    />
```

These directives defines the binding between Bill and the type of available forms: DisplayForm, AddForm, EditForm, DeleteForm.

An example of configuration, binding a model with a generic ’view’, is in bungeni.ui views.zcml:
```
    <browser:page name="workflow"
        for="bungeni.models.interfaces.IBungeniContent"
        class=".workflow.WorkflowView"
        permission="zope.Public"
    />
```

This configuration is generic and valid for all contents implementing the IBungeniContent interface.

The order of the field is basically the order in which they are defined in the ModelDescriptor class. The most straight forward way to change the order is to subclass the form and select the fields in the order you like to have but as it is a zope.form field the field has an order which you may set too. If the ordering should be done in a configuration file that is probably the way to go

### How to override a view

!+_WARNING_(mr, oct-2010) content below here is outdated.

To change a default view the configuration must re-define the association between the model and the brower page. In Zope the usual way is to create an overrides.zcml file, for example:
```
<browser:pagename="view"for="bungeni.models.interfaces.IBill"class=".myviews.DisplayForm"permission="zope.View"layer="bungeni.ui.interfaces.IBungeniSkin"/>
```

In this declaration we substitute the “.common.DisplayForm” with the “.myviews.DisplayForm”. The ’overrides.zcml’ file of our package must be loaded at startup time declaring the override in site.zcml configuration. For example the repoze.whooze package needs:
```
<includeOverrides package="repoze.whooze" file="overrides.zcml" /> 
```

## Add security permissions to a form

The permissions are put in the zcml files together with the browser pages declarations. In case of forms they are in bungeni.ui.forms forms.zcml and configure.zcml. The permissions for the forms are setup with the keyword ’permission’. The available permissions are scattered in many packages but in these two files you found the most used. If you need to define new one they are defined as:

```
<permissionid="zope.View"title="[view-permission] View"/> 
```

## Add a page to display a listing of contents

The views for the container listings are defined in bungeni.ui container.py module. The base class is ContainerListing and the main implementation is in tables.py: AjaxContainerListing, this class is configured with:

```
<browser:pagefor="ore.alchemist.interfaces.IAlchemistContainer" 
	permission="zope.Public" 
	name="index" 
	menu="context_actions" 
	title="Listing" 
	template="templates/generic-container.pt" 
	class=".table.AjaxContainerListing" 
	layer="bungeni.ui.interfaces.IBungeniSkin"/>
```

it means each container use this implementation, so in general for new contents and its containers it is not need to define a new class to manage the listing. In case one must create a new configuration declaring a more specific ’for’ parameter and the class implementing the “formatter” method.