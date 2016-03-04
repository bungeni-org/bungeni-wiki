

# 2010 Redesign Rationale: Localization of Bungeni Forms

## Requirements

### General Assumptions

We accept the following assumptions to set the scope and limits of what to allow as form localizations in Bungeni:

**`[A0]`** All statements here are _Field_-oriented i.e. everything stated is within the context of a specific Field (within a UI Descriptor).

**`[A1]`** Every localizable mode has a default localization i.e. for each localizable mode there is a bungeni defined default set of roles for whom the field is to be shown. OTOH, every non-localizable mode may **not** be associated to a set of roles for visibility (the related security declarations in domain.zcml must prevail).

  * Besides the obvious scenario (of declaring a mode localizable and specifying the roles the Field is to be shown for) this also enables the possibility to declare a mode to be localizable while by default keeping it not visible to any role (an example of this case is the `ParliamentaryItemDescriptor.full_name` field, that is declared but made non-visible for all roles, leaving it up to a parliament to enable it if so desired).

**`[A2]`** Some fields are **required** for proper functioning of Bungeni (e.g. by the data schema, or are assumed to be there by a workflow definition) and thus may not be disabled/hidden i.e. may not be localized--at least in certain key views, such as a form in `add` and `edit` modes, to ensure that the schema/business constraints are always respected. As a consequence, a mode may be declared not localizable in Bungeni and any attempt to localize it for a specific deployment must result in an immediate error.

**`[A3]`** The display order of fields in all forms is **always** localizable; furthermore the relative order of displayed fields will always be the same for all modes (and for all roles).

**`[A4]`** If a mode is declared to be localizable, then it is localizable for all roles.

**`[A5]`** Visibility of a field in any mode, localizable or not, is always **subject to** the privileges that the user has on the domain object's attribute that the field corresponds to. (The general case is that a field is bound to a model object's attribute, but this is not necessarily **always** the case; what should the behaviour be in such cases?).

### Extension of Bungeni UI Descriptor API

To support localization, some changes to how forms are processed internally within Bungeni are needed. These consist mostly in adding the current user's role as an additional modifier of what fields are shown or hidden per form mode.

We retain the Field-oriented descriptor mechanism that is currently in place, and evolve it to cover the additional needs, that thus consist of:

**`[B1]`** To be able to declare which **modes** a field is **allowed to be localized in**.
<br />Note: this feature is in addition to and independent of the already implemented ability of declaring which modes a field is to be displayed in.

**`[B2]`** For each localizable mode, to be able to declare which **roles** the field is to be **displayed for**.

Some changes to bungeni/alchemist code are required to process these additional declarations.


### Localization by a Parliament's Site Admin

To support localization of a deployment's form views the following is required:

**`[C1]`** To be able to state, per localizable UI descriptor, the display order (and inclusion) of all fields (in their entirety and independently of all modes/roles for which they may be enabled).

  * It is an error to omit **any** field. To categorically disable a field from being displayed it must still be included but declared to be hidden from all roles (for all modes it is localizable in).
  * Localization of a descriptor's list of fields is all or nothing: no default value for the list of fields is to be assumed here--if a localization declares a descriptor, then that descriptor declaration must explicitly include the full list of fields.

**`[C2]`** To be able to adjust, per localizable mode, the roles for which the field is to be displayed for.

  * It is an error to attempt to localize a non-localizable **descriptor**.
  * Contrarily, it is perfectly OK for a localization to omit entirely a localizable **descriptor** (implying that the omitted descriptor is simply not being localized in this deployment).
  * It is an error to attempt to adjust a **field**'s roles for a non-localizable mode.
  * Localization of a field's visibility modifiers is all or nothing: if a field specifies **any** visibility modifiers (hide/show), then these will **replace** whatever is defined in stock Bungeni; on the other hand, if a field does **not** speficy any visibility modifiers, then whatever is defined in stock Bungeni is retained as the default.
  * Localization of mode's roles is all or nothing: for a **field** in a localizable mode, if the bungeni-specified list of roles is to be adjusted then the list of roles must be entirely restated i.e. the entire desired list of a mode's roles must be explicitly specified, either via explicit inclusion (show) or as the complement of explicit exclusion (hide).


### Other considerations

**`[X1]`** To facilitate the site admin's task of localizing a bungeni deployment, the Bungeni system should automatically generate a default configuration file, reflecting precisely all the display directives for all form fields (per mode per role).

**`[X2]`** The source format selected for the configuration file should support easy commenting out of entire declarations or parts thereof.

**`[X3]`** How a localization is picked by the system should be convenient--such that switching from one localization to another involves no more than simply modifying a file-system path parameter in the application's deploy.ini file and restarting the application.


## Design

### Descriptor API

#### `bungeni.alchemist.model.ModelDescriptor`

##### `ModelDescriptor.localizable:bool = False`

As not every descriptor corresponds to a actual set of UI form views, content-type descriptors must explicitly be declared as localizable.

Example is the base descriptors that are used for programmatic convenience to define other real descriptors e.g. `ParliamentaryItemDescriptor` does not directly correspond to a set of views.

To address this, we add a `localizable:bool = False` class attribute to `ModelDescriptor`, like so:

```
    class ModelDescriptor(object):
        localizable = False
```

Note that localization of individual Fields is completely independent of localizability of a descriptor i.e. a field may be declared localizable even when the containing descriptor is declared not localizable.

#### `bungeni.alchemist.model.Field`

The meaning of the current `modes` class attribute stays unchanged.

##### `Field._roles`

We first need to state precisely what roles may be targetted by localization--by adding
the following attribute on the Field class:

```
    # The list of roles exposed to localization
    _roles = [
        #"bungeni.Admin", # parliament, has all privileges
        "bungeni.Clerk", "bungeni.Speaker", 
        #"bungeni.Owner", # instance
        "bungeni.MP", # parliament 
        "bungeni.Minister", # ministry 
        #"bungeni.Translator", # parliament
        #"bungeni.Authenticated", # all authenticated users, all above roles
        "bungeni.Anonymous" # unauthenticated user, anonymous
    ]
```

This set of (uncommented) roles is thus an **explicit subset** of all `bungeni.*` roles defined within the system (that typically are defined in `models/roles.zcml`).

This list of roles is also what is to be used as the fallback default value whenever a list of roles is expected and not specified.

##### `Field.localizable:sequence = []`

We then need to conveniently state which modes the field is localizable in, and what is the default list of roles it is to be shown to -- we add a `localizable` parameter on the Field class, with the allowed value being:
```
        [ either(show(modes, roles), hide(modes, roles)) ]
```
i.e. a list of either `show(modes, roles)` or `hide(modes, roles)` declarations, where the modes/roles keyword parameters are each a list of parameters, with the following constraints:

  * the field is localizable for a given mode if (and only if) that mode appears in one (and only one) show/hide declaration--this also implies that a mode may appear only once in a field's show/hide directives.

  * in a show/hide directive, not specifying either of the `modes` or `roles` parameter is equivalent to specifying, for `modes=None`, all the displayable modes specified by the field instance and, for `roles=None` all the roles supported by the Field class i.e. what is set in `Field._roles`. A corollary of this is that an additional `show()` declaration (with the modes parameter omitted) **must** generate an error (as this means at least a mode is included more than once in the show/hide declarations).

  * directives `show` or `hide` are _roles-complement_ of each other i.e. assuming the same list of roles exposed to localization as above, the following are two equivalent statements of the same rule (for each case, convenience and improved clarity would inform which to prefer):
```
    hide(roles="bungeni.Anonymous")
    show(roles="""bungeni.Clerk bungeni.Speaker bungeni.MP bungeni.Minister""")
```

  * the implementation of `show` and `hide` consists of a couple of factory utilities each having the 2 keyword parameters, `modes` and `roles`.

  * the default value (i.e. an empty list) of the `localizable` Field attribute implies that the field is not localizable in any mode and to be localizable a Field instance must explictly reset the value of its `localizable` parameter.

  * the Field instance may internally transform this list of show/hide directives, for more optimized processing.

Example:

```
    class ParliamentaryItemDescriptor(ModelDescriptor):
        localizable = False
        fields = [
            ...,
            Field(name="owner_id",
                modes="view edit add listing",
                localizable=[
                    hide("view listing", "bungeni.Anonymous"),
                ],
                property=schema.Choice(title=_(u"Moved by"),
                    description=_(u"Select the user who moved the document"),
                    source=vocabulary.MemberOfParliamentDelegationSource("owner_id"),
                ),
                ...
            ),
            ...
        ]

    class QuestionDescriptor(ParliamentaryItemDescriptor):
        localizable = True
        ...
        fields = [
            ...,
            Field(name="ministry_submit_date",
                modes="view edit",
                localizable=[ show("view") ],
                ...
            ),
        ]
```
The above code declares the following:

  * `QuestionDescriptor` as localizable.
  * The `moved_by` question field to be localizable in the `view` and `listing` modes (and only those modes), while the `ministry_submit_date` question field to be localizable in the `view` and `edit` modes (and only those modes).
  * For `moved_by`: for the 2 modes, it sets the default lists of roles that this field is to be shown for to be all roles except for `bungeni.Anonymous`.
  * For `ministry_submit_date`: it states that in `view` mode the field is to be shown for all roles.


Note the following is an equivalent way to express the `moved_by` field `localizable` init parameter as the one in the example above:
```
            Field(name="owner_id",
                modes="view edit add listing",
                localizable=[
                    hide(modes="view", roles="bungeni.Anonymous"),
                    hide(modes="listing", roles="bungeni.Anonymous"),
                ],
                ...
            ),
```
but the following would be an error (because `listing` mode appears more than once in the fields's localizable directives):
```
            Field(name="owner_id",
                modes="view edit add listing",
                localizable=[
                    show(modes="listing", roles="bungeni.MP bungeni.Clerk"),
                    hide(modes="view listing", roles="bungeni.Anonymous"),
                ],
                ...
            ),
```


In the example below, omitting both the `modes` and the `roles` parameters (or equivalently specifiying them as `None`) in the call to `show()` will (a) enable the field for the field's list of displayable modes i.e. for `view` and `edit` in this case, and (b) enable the field for all roles:
```
            Field(name="ministry_submit_date",
                modes="view edit",
                localizable=[ show() ],
                ...
            ),
```

### Localization API

If a descriptor is to be included in a localization, then all the fields are to be listed (as stated in requirements)--making the configuration of the **order** of appearance immediate, straighforward, and maintainable even by someone not especially familiar with bungeni.

To continue with the example above, let's look at what a localized Question descriptor would look like (here we assume an XML syntax, but this could just as easily be JSON or other data format).

The declaration structure follows the same lines as descriptor's Field.localizable. Let's assume that the only localizable (and localized) Fields in this descriptor are the `moved_by` in `view` and `listing` modes and `ministry_submit_date` in `view` mode:
```
<ui roles="bungeni.Clerk bungeni.Speaker bungeni.MP bungeni.Minister bungeni.Anonymous">
    ...
    <descriptor name="Question">
        <field name="parliament_id" displayable="view edit" localizable="view" />
        <field name="short_name" displayable="view edit add listing" localizable="view listing" />
        <field name="full_name" displayable="view edit add" localizable="view edit add" />
        <field name="registry_number" displayable="view edit" localizable="view" />
        <field name="owner_id" displayable="view edit add listing" localizable="view listing">
            <show modes="view" roles="bungeni.Clerk bungeni.Speaker" />
            <hide modes="listing" roles="bungeni.Anonymous" />
        </field>
        <field name="language" displayable="view edit add" localizable="view" />
        <field name="body_text" displayable="view edit add" localizable="view" />
        <field name="submission_date" displayable="view listing" localizable="view listing" />
        <field name="status" displayable="view edit listing" />
        <field name="status_date" displayable="view listing" localizable="view listing" />
        <field name="note" displayable="edit add" localizable="" />
        <field name="receive_notification" displayable="view edit add" />
        <field name="question_number" displayable="view listing" />
        <field name="ministry_id" displayable="view edit add listing" />
        <field name="admissible_date" displayable="view listing" localizable="view listing" />
        <field name="ministry_submit_date" displayable="view edit" localizable="view">
            <show modes="view" />
        </field>
        <field name="question_type" displayable="view edit add" />
        <field name="response_type" displayable="view edit add" />
        <field name="response_text" displayable="edit" />
    </descriptor>
    ...
</ui>
```
The above localization code is:

  * setting the field order.
  * stating that the `owner_id` field (+!`Moved by`--the (untranslated) human property title (i18n text id) should probably be used instead of the underlying attribute name) is being localized to be displayed for Clerk/Speaker in view mode, and to be shown to all except for `bungeni.Anonymous` in listing mode.
  * localizing the `ministry_submit_date` Field in `view` mode to not be shown to any role.

Other remarks about the above localization code sample:

  * We specify **all** the fields (those defined in `QuestionDescriptor` as well as those copied off from `ParliamentaryItemDescriptor`)--to change the order we simply... change the order.
  * The show and hide directives follow exactly the same logic as in bungeni descriptor.
  * A show/hide that does not specify any roles means it specifies all roles... a show/hide directive that does not specify any roles attribute (or an empty one) is equivalent to specifying the full set of targetted roles i.e. those specified as the `@roles` attribute on the `<ui>` root element.
  * the `field/@modes` and `field/@localizable` attributes are specified (these should typically be automatically generated) for information only, to respectively state the modes a field is displayable and, if any, the modes the field is localizable in (must be a subset of the field's displayable modes).

As suggested in the requirements, a localization file with all localizable descriptors and their default settings should be automatically generated by bungeni.

Should it ever become necessary, it is forseeable, using this syntax, that the `<show>` and `<hide>` directives here could gain further modifier attributes, such as one to state additional conditions e.g. an object's workflow states.
