# Authentication Stack

The repoze.whooze packages handles identification and authentication, it gets identity and authentication information via repoze.who.

All bungeni principals are repoze.whooze.auth.WhoPrincipal objects which implement the zope.security.interfaces.IGroupAwarePrincipal interface.

Groups are principals and can be assigned permissions as well(that is why groups have a group\_principal\_id attribute in the orm).

When a group is created, it is granted the permissions defined for that group in the zcml files eg. the clerks office gets bungeni.Clerk permission ( See [bungeni.core/workflows.groups.py](http://bungeni-portal.googlecode.com/svn/bungeni.main/trunk/bungeni/core/workflows/groups.py) and [bungeni.core/workflows/utils.py](http://bungeni-portal.googlecode.com/svn/bungeni.main/trunk/bungeni/core/workflows/utils.py) and [bungeni.buildout/security.zcml](http://bungeni-portal.googlecode.com/svn/bungeni.buildout/trunk/security.zcml) ) .

When a user is added to the group, they get the permissions defined for that group since they are "group aware" as stated above, in addition to permissions that may have been granted explicitly to that principal.

Groups are added to a principal by the repoze.who authenticator bungeni.portal.auth.AlchemistWhoPlugin.


# System setup admin

In a new installation there are no groups and no permission assignments as yet. The admin user is added using the admin-password.py script in the data/scripts folder.