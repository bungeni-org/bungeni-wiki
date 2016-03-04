# Bungeni Programming Guide

Rules, conventions, guidelines for programming bungeni, to increase consistency, efficiency and maintainability of the bungeni code base.

These rules are primarily concerned about design, architecture, adoption of selected "best practices". Souce code style guidelines are addressed in a separate document [Bungeni Source Code Style Guide](DevCodeStyleGuide.md).


---


## Misc

  * Widely established conventions within the python world should be respected.
  * The bungeni python package is not a namespace package.


---

## Security

Description of how roles and permissions should be defined and assigned:
a "programming security policy" that should be considered as an integral
part of the system design, and any other ongoing design choice **must**
consider how it interacts with or affects the "security policy" itself.

### Security definitions (roles, permissions, grants)

These are (currently) made in several places, namely:

```
bungeni.main/bungeni/security.zcml
bungeni.main/bungeni/models/roles.zcml
bungeni.main/bungeni/models/permissions.zcml
bungeni.main/bungeni/core/workflows/permissions.zcml (automatically generated via bungeni_custom/workflows/...)

bungeni_custom/workflows/...

bungeni_custom/sys/acl/permissions.zcml
bungeni_custom/sys/acl/roles.zcml
bungeni_custom/sys/acl/meta/...
bungeni_custom/sys/acl/content/...
```



Where, what, and how to define roles/permssions/grants should be clarified...
Some reflection as to support for:

  1. user-owned types (always workflowed), with dedicated permissions for various actions (each action possibly being dependent on a feature being enabled or not); type-dependent permissions should be defined/granted along with type definitions themselves
  1. system-owned types (may or may not be workflowed) with dedicated permissions, that are exposed "within limits" to some user customization
  1. or, system-owned types that are NOT at all exposed to user customization (completely internal to bungeni)
  1. support for user-defined roles, and for assigning permissions to them...
  1. UI views (typically require globally granted permissions) should use dedicated permissions, and so different than any related model-bound permissions.

!+most of `sys/acl/...` should not be there; question.zcml is already "orphan code"; why both `/meta` and `/content` sub-folders?

#### Security requirement statements

These are done in:

```
bungeni.main/bungeni/models/domain.zcml
bungeni.main/bungeni/ui/configure.zcml
...
```

!+identify a simple practice for this


### Security and permission guidelines

#### Permissions are only granted to **roles**

Bungeni categorically grants/denies permissions only to **roles** i.e. never directly to a principal (or group). And, to reduce runtime overhead, the `BungeniSecurityPolicy` actually does not perform the check for any direct permission grants on principals and groups.

#### Grant a permission only when needed
Grant a permission only when needed i.e. minimize global grants. A premise of Zope 3 security is that "Access is disallowed unless explicitly allowed" (see [Zope3SecurityModel](http://wiki.zope.org/zope3/Zope3SecurityModel)). Bungeni should adopt a similarly-spirited approach--in particular, Bungeni should **disfavour global grants** to then deny as needed downstream (this would, as practice, actually translate to something like "Access is allowed unless explicitly disallowed"!). Instead, Bungnei should, whenever possible, prefer the general practice of simply granting only when needed.

#### Permission Naming

All permission names should have the form:
```
    bungeni.{object[ .subobject ]}.{Action}
```
where :
  1. _object_ is a **noun** indicating the target type of object (all lowercase, may optionally have additional dot-separated subtypes).
  1. _Action_ is a **verb** indicating the permitted target action (capitalized).

#### No redefinition of permissions
The previous practice to redefine `{TYPE}.View` permissions to the generic `zope.View` carries more pitfalls (e.g. confusion from when is it defined, and subsequent grants of either of the permission) than promised convenience gain.

Plus, as the system becomes more and more declartive/dynamic, with type-specific permissions being inferred automatically, there is even less need to redefine permissions (when needed, also just infer the one explicit permission). So, explicit permission id, used consistently, so source code can be understood/debugged using simple string searching.


#### Distinct UI and Type permissions
UI permissions are necessarily **always granted globally**, while `type` permissions _almost always_ need to be granted locally. There should be zero overlap between permissions used for UI elements and components, and permissions used for actions on domain data objects.


#### Distinct permission _scopes_
For clarity of intention, permission _scopes_ should be kept as distinct as possible. Notably, the "permission scope spaces" implied by `bungeni.Authenticated` and those implied by other "custom" roles (all of which imply `bungeni.Authenticated`) overlap. Grants to roles from these two sets should not be mixed, as:

  * Debugging permission checking is made unnecessarily more difficult; indeed understanding what a permission setting should be by reading the code becomes virtually impossible.
  * It just invites for developer inconsistency.
  * This inconsistency will result in incorrect decisions in some cases e.g. View on a change record of an attachment to a a document may be granted to a member virtue of Authenticated having zope.View (be it globally or locally on the attachment instance or on any other ancestor instance).

I propose adoption of the following policy to keep permission determination as simple as possible, to never mix applying permissions in this way (where what is to be not mixed would be similar, if more stringent, to what has been implemented for workflow permission assignments, from r8919 (on Google Code) on):

> For a given specific permission, the "scope" for that permission is either limited to
> `bungeni.Authenticated` OR ONLY to the set of other custom roles.
> I.e. if we consider two mutually exclusive sets of roles:
```
    A: bungeni.Authenticated
    B: bungeni.Clerk bungeni.Speaker bungeni.Owner bungeni.Signatory bungeni.MP bungeni.Minister
```
> then the "permission space" we can "assign" in would be:
```
    {(permission, object), role, setting)}
```
> where, for any (permission, object) pair, assignments (irrepective of setting) of the permission
> may only be to roles **only** within _either_ A _or_ B **anywhere** in the application.
> Further, if a permission is assigned globally (here, object=None would mean global)
> then it _should_ not ever be assigned locally.

> I.e. assignments of a given permission must **always be to only one** of
> the following 4 combinations: `{global, local} x {A, B}`



---

## ZCML

Given the liberal misuse and overuse of ZCML to date, rendering the code base a lot more opaque than necessary, increasing debugging overhead, increasing refactoring overhead, creating numerous cyclic but never executed interdependencies between code, and other code entropy, there should be no new ZCML declaration added to the bungeni code base.

Other than purely security-related declarations (advantages of doing this inline are not as strong as for other types of components), all registrations on the Zope Component Registry should be done in python.

The decorator utilities available in the module `bungeni.ui.register` should be used to register all software components. If any functionality is missing in that module, it should be added as needed.


---

## Views and Viewlets

To promote consistency, facilitate debugging and management, all views and viewlet classes must inherit, respectively, from the bungeni base classes
```
bungeni.ui.browser.BungeniBrowserView
bungeni.ui.browser.BungeniViewlet
```
These base classes offer an easy hook for centrally adding handling of views and viewlets, such as special debug mode output, common names for common view attributes such as title, etc.


### Templates

  * Templates should be as dumb as possible, concerning themselves with purely presentational logic; templates should need to have **no** "business" knowledge of data received for rendering.

  * Localization of any data is done **outside** of templates. This to make it clear where data **should** be localized, avoiding the frequent occurrences of data either _not being localized_ at all or data _being localized twice_ (in application python code and in the templates). And, as localization cannot be guaranteed to **always** be performed within the templates, then it should **always** be done in application code, outside of the templates.

  * Localization of data should be done as late as possible. This is for two reasons:
    1. retain the data in its most semantic state as long as possible, to never lose for example the option to do proper numeric or date arithmentic, or proper string interpolation.
    1. to ensure that the localization takes into proper account the user's current locale!


<a href='Hidden comment: 
'></a>
