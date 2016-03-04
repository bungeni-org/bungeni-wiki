

# Introduction

Bungeni forms and listings can be customized using an xml configuration file, and this page documents how to do precisely this.

# forms/ui.xml

The main access point to customize forms and listings is the `forms/ui.xml` file, located within the bungeni customization folder i.e. if your bungeni customization folder is in `src/bungeni_custom` - then this file can be found under `src/bungeni_custom/forms/ui.xml`.

All the information in ui.xml is contained within the `<ui>` element.

```
<ui roles="bungeni.Clerk bungeni.Speaker bungeni.MP bungeni.Minister bungeni.Anonymous">
    ...
    <descriptor ... />
    <descriptor ... />
    ...
</ui>
```

The `<ui>` element specifies an `@roles` attribute that defines the list of roles that may participate in the localization. The value of `ui/@roles` may be modified as needed e.g. to add other roles that you may have defined in your deployment. An additional function of `ui/@roles` is that it also serves as the default value for all unspecified `@roles` attributes on other elements in the same XML document--those that support a `@roles` attribute of course i.e.. the `<show>` and `<hide>` elements.


## Forms

Forms in Bungeni are used to add, view, edit or list Bungeni content. In `ui.xml` all the forms are defined within the `<ui>` element. E.g.

```
<ui roles="bungeni.Clerk bungeni.Speaker bungeni.MP bungeni.Minister bungeni.Anonymous">
    ...
    <descriptor name="AgendaItem">
        <field name="parliament_id" displayable="view listing" localizable="view listing">
            <hide modes="view listing" />
        </field>
        ...
    </descriptor>
    
    <descriptor name="Question">
        <field name="parliament_id" displayable="view listing" localizable="view listing">
            <hide modes="view listing" />
        </field>
        ...
    </descriptor>
    ...
</ui>

```

A Form specification in bungeni is called a **descriptor**, and its localization is specified via a `<descriptor>` element. The `descriptor/@name` attribute identifies the name of the Form.


## Form Modes

Bungeni forms support presenting content in four different modes:

  * **add** - When new content is being added. e.g. if a member of parliament is adding a new question.
  * **edit** - When existing content is being edited . e.g. if a clerk is editing a question submitted by a Member of parliament.
  * **view** - When existing content is being viewed. e.g. if a public user is viewing a question on Bungeni.
  * **listing** - This mode is used when listing the content. e.g. if you go into the the page listing all the questions in the system, whether a question is displayed depends on the rules defined for this mode.


## Form Fields

Fields on a form are identified by the `<field>` element.

Below are some of the fields in a **Question** form.

```
    <descriptor name="Question">
        ...
        <field name="short_name" displayable="view edit add listing" localizable="view edit listing">
            <show modes="view edit listing" />  <=== short name (title) field
        </field>
        <field name="full_name" displayable="view edit add" localizable="view edit add">
            <show modes="view edit add" />      <== full name (summary) field 
        </field>
        <field name="registry_number" displayable="view edit listing" localizable="view edit listing">
            <show modes="view" />
            <hide modes="edit listing" />       <=== registry number (not shown in edit)
        </field>
        <field name="owner_id" displayable="view edit add listing" localizable="view listing">
            <hide modes="view listing" roles="bungeni.Anonymous" />
        </field>
        <field name="language" displayable="view edit add listing" localizable="view edit listing">
            <show modes="view edit" />
            <hide modes="listing" />
        </field>
        <field name="body_text" displayable="view edit add" localizable="view">
            <show modes="view" />
        </field>
        ...
    </descriptor>
```

Each of these corresponds to a field on a Question form:

![http://bungeni-portal.googlecode.com/files/add_question.png](http://bungeni-portal.googlecode.com/files/add_question.png)

The behavior of form fields is controllable via a combination of roles and [modes](#Form_Modes.md).

The `<field>` element has the following **information-only** attributes i.e. **you are NOT allowed to change the values of these attributes**:

  * **name** - the `field/@name' attribute identifies the name of the field:
```
        <field name="short_name" ... />
```
  * **displayable** - the `field/@displayable` attribute identifies in which [modes](#Form_Modes.md) the field **may** be displayed i.e. subject to internal Bungeni system logic (e.g. user has the required permission, the field is validly set, etc) this field may be displayed in this mode.
  * **localizable** - the `field/@localizable` attribute identifies the actual modes in which [modes](#Form_Modes.md) the (displayable) field **may** be localizable i.e. you may control for whom this field should be made to appear in this mode. The list of localizable modes is necessarily always a sub-set of those listed in `field/@displayable`.

Let us explain the difference between `field/@displayable` and `field/@localizable` using an example:

The following states that the `registry_number` field **may** appear in "view edit listing" modes; and additionally it **can** also be localized in "view edit listing" modes.
```
        <field name="registry_number" 
            displayable="view edit listing"  <<-- modes in which field MAY be displayed
            localizable="view edit listing"  <<-- modes in which displayable field CAN be localized
        >
```

The following states that the `registry_number` field **may** appear in "view edit listing" modes; but additionally it **can** pnly be localized in "view" mode.
```
        <field name="registry_number" 
            displayable="view edit listing" <<-- modes in which field MAY be displayed
            localizable="view"              <<-- modes in which displayable field CAN be localized
        >
```


## Field Customization

Fields are customized using `<show>` and `<hide>` specified within a `<field>` element.
These (in addition to field _order_) are the **ONLY** part of the Forms xml configuration which is editable by the user.

`<show>` and `<hide>` do exactly what they are named as, `<show>` shows the field for the listed modes and roles, and `<hide>` hides the field for the listed modes and roles. A field may specify as many `<show>` and/or `<hide>` directives as it needs.

Both these elements have the following attributes that allow customization of the field behavior:

  * **modes** - lists the modes that are to be shown or hidden. The only modes allowed here are the ones listed in the `field/@localizable` attribute.
  * **roles** - lists the roles for which the modes are to be shown or hidden. The only roles allowed here are the ones listed in the `ui/@roles` attribute.

There are some simple rules to follow while using `<show>` and `<hide>`:
  * The only modes available for use are the ones provided by the `field/@localizable` attribute.
  * A mode may only appear once i.e. either in a `<show>` or or in `<hide>` directive.

The usage of `<show>` and `<hide>` is explained below, via examples --

The following hides the `owner_id` field in "view listing" modes for the "bungeni.Anonymous" user.
```
        <field name="owner_id" displayable="view edit add listing" localizable="view listing">
            <hide modes="view listing" roles="bungeni.Anonymous" />
        </field>
```


The following hides the `owner_id` field in "listing" mode for the "bungeni.Anonymous" user; and makes the field available in "view" mode for all users (including "bungeni.Anonymous" ).
```
        <field name="owner_id" displayable="view edit add listing" localizable="view listing">
            <hide modes="listing" roles="bungeni.Anonymous" />
            <show modes="view" />
        </field>
```

The following is **incorrect**, because "listing" appears in more than one `<show>` or `<hide>` directives.
```
        <field name="owner_id" displayable="view edit add listing" localizable="view listing">
            <hide modes="listing" roles="bungeni.Anonymous" />
            <show modes="view listing" />
        </field>
```

The following hides the `owner_id` field in "listing" mode for the "bungeni.Anonymous" user; and makes it visible in "view" mode **only** for users with the "bungeni.Clerk" role.

```
        <field name="owner_id" displayable="view edit add listing" localizable="view listing">
            <hide modes="listing" roles="bungeni.Anonymous" />
            <show modes="view" roles="bungeni.Clerk" />
        </field>
```

## Field order Customization

The order in which the fields are presented on a form can be customized by changing the order of fields within a descriptor. Changing the order of fields will be applicable to all modes.

For example, the `registry_number` below appears after the `short_name`:

```
  <descriptor name="Question">
        <field name="parliament_id" displayable="view listing" localizable="view listing">
            <hide modes="view listing" />
        </field>
        <field name="short_name" displayable="view edit add listing" localizable="view edit listing">
            <show modes="view edit listing" />
        </field>
        <field name="full_name" displayable="view edit add" localizable="view edit add">
            <show modes="view edit add" />
        </field>
        <field name="registry_number" displayable="view edit listing" localizable="view edit listing">
            <show modes="view" />
            <hide modes="edit listing" />
        </field>
        ...
  </descriptor>
```

The registry\_number field was moved up to be the first field on the form --
```
    <descriptor name="Question">
        <!-- registry_number below was moved up to be the first field -->
        <field name="registry_number" displayable="view edit listing" localizable="view edit listing">
            <show modes="view" />
            <hide modes="edit listing" />
        </field>
        <field name="parliament_id" displayable="view listing" localizable="view listing">
            <hide modes="view listing" />
        </field>
        <field name="short_name" displayable="view edit add listing" localizable="view edit listing">
            <show modes="view edit listing" />
        </field>
        <field name="full_name" displayable="view edit add" localizable="view edit add">
            <show modes="view edit add" />
        </field>
        ...
    </descriptor>
```

Henceforth, the `registry_number` field (if at all displayed) is always displayed first on the form.

## More examples of show/hide combinations

For a field described like this:

```
        <field name="owner_id" displayable="view edit add listing" localizable="view listing">
            <hide modes="listing" roles="bungeni.Anonymous" />
            <show modes="view" roles="bungeni.Clerk" />
        </field>
```

The following are some examples of show/hide combinations for the above field:

  * Hide in listing mode for anonymous, show in view mode for Clerk:
```
    <hide modes="listing" roles="bungeni.Anonymous" />
    <show modes="view" roles="bungeni.Clerk" />
```

  * This is **incorrect** and will trigger a `BungeniCustomError` - it states to show in `edit` mode to the Clerk, but that mode is not localizable here:
```
    <hide modes="listing" roles="bungeni.Anonymous" />
    <show modes="view edit" roles="bungeni.Clerk" />
```

  * This is **incorrect** and will trigger a `BungeniCustomError` - it states hide in ALL modes (inlcuding `view`) for Anonymous, but it also states to show in `view` mode (thus, repeats this mode that is not allowed) for the Clerk:
```
    <hide roles="bungeni.Anonymous" />
    <show modes="view" roles="bungeni.Clerk" />
```

  * This is **incorrect** and will trigger a `BungeniCustomError` - it states hide in ALL modes (here `view listing`) for the MP, but show in `listing` mode (thus, repeats this mode that is not allowed) for all Roles:
```
    <hide roles="bungeni.MP" />
    <show modes="listing" />
```
> > that would be just like stating:
```
    <hide modes="view listing" roles="bungeni.MP" />
    <show modes="listing" roles="bungeni.Clerk bungeni.Speaker bungeni.MP bungeni.Minister bungeni.Anonymous" />
```

  * This is **incorrect** - it states hide in ALL modes for MP (here `view listing`), but also show in ALL modes (thus, repeated modes) for all Roles:
```
    <hide roles="bungeni.MP" />
    <show roles="bungeni.Clerk" />
```
> > that would be just like stating:
```
    <hide modes="view listing" roles="bungeni.MP" />
    <show modes="view listing" roles="bungeni.Clerk" />
```


## Restoring ui.xml

It is good practice to make a backup copy of ui.xml whenever you edit it.

However, you can also restore the default localization settings for bungeni form descriptors by running:
```
./bin/restore-localization-forms-defaults
```
from the buildout folder.
This will regenerate the `forms/ui.xml` from the source code definition of the forms.

