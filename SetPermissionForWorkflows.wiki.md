# Introduction

> Note: the permissions who can initiate a workflow transition is defined
> in the workflow itself + in the roles and grants.zml for the workflow,


## Get the user id for the current user

```
from zope.security.management import getInteraction
from zope.publisher.interfaces import IRequest

def getUserId( ):
       interaction = getInteraction()
       for participation in interaction.participations:
            if IRequest.providedBy(participation):
                return participation.principal.id
```


so currently lots of permissions have been defined corresponding to every
transition. thats probably overkill versus doing some generic permissions
for use across workflows and types, but its fine. the permission definitions
and default **global** grants should stay in the zcml.  the action handlers on
the workflow will change to include functionality to manipulate the workflow
using the local role perm map and local role principal map api, depending on
the requirements.


# Details

there are two facilities for local security manipulations, role permission
maps, and principal role maps. to enable this for a given object the object
should implement alchemist.security.ISecurityLocalPrincipalRoleMap or
ISecurityLocalRolePermissionMap which is defined for all IBungeniContent.

the local and global versions of these maps behave exactly the same. the
alchemist.security doctests go through the api available. also keep in mind
that you can deny a permission or a role mapping at a local level that will
take precendence over a global one or a mapping further up the containment
chain.



>
> e.g.:
>
>
> a general permission for the role 'Member of parliament' create
> question.
>
> an MP creates a question. (id = 1234)
>
> after adding (saving) the question in the transition 'create' the MP who
> submitted the questions gets the role 'owner' for the object\_type =
> 'Question' object\_id=1234
>

so the action handler for the transition gives this user the Owner local
role via the local role principal role map.
```
zope.securitypolicy.interfaces.IPrincipalRoleMap( question ).assignRoleToPrincipal(role_name, qualfied_user_id)
```

> the role owner gets the
> permissions 'edit', 'view' and 'delete' for the object\_type 'Question'
> object\_id '1234'
>


those permissions should be defined globally for the owner role, imo.


>
> workflow transition 'submit to clerk' by MP
>
> the permissions 'edit' and 'delete' is set to false for the role owner
> for object 'Question' id '1234' , (so he still has the view permission).
>


>
> the permission 'view' = true is added  for the role:'clerks office' for
> object\_type 'Question' object\_id '1234' ( no other permission as the
> permission to initiate the workflow transition 'recieve' is defined in
> the workflow itself.)
>

so now we use the rolepermission map local in an action handler

grant the new permissions to the clerks office
```
 zope.securitypolicy.interfaces.IRolePermissionMap( question ).grantPermissionToRole( permission_name, role_name )
```
remove the permissions for editing, deleting from the owner
```
 zope.securitypolicy.interfaces.IRolePermissionMap( question ).denyPermissionToRole( permission_name, role_name )
```




**in zope\_principial\_role\_map if object\_type, object\_id is NULL => It is a global role**



yes, if your getting it via adapation on content the adapter will take care
of setting these and offering the api.

you should use the api for manipulating these tables, do not attempt to
directly manipulate the table, use the api.


  * in zope\_principial\_role\_map if object\_type is NULL => It is a global role for this Object\_type
  * if object\_type, object\_id is NULL => global permission
  * object\_type and object\_id in these tables are used for local roles



## Setting permissions on Child Objects

responses can only be added to questions in a certain state - so
the response container should not allow for responses to be added if the
question does not have that state. You may simply set the permission 'add response' on a question object and the setting will be acquired.