## 1.8 Permissions & Role definitions

General documentation:  http://wiki.zope.org/zope3/secureobjects.html

In bungeni the specific permissions for contents (add, edit, delete,...) are
defined in bungeni/models/permissions.zcml. Workflow transition permissions are
defined in bungeni/core/workflow/permissions.zcml
A definition is in the form:
```
    <permission id="bungeni.question.Submit" title="Submit a question" />
```
Roles of Bungeni are defined in bungeni/models/roles.zcml, the definition is as:
```
    <role id="bungeni.Admin" title="Bungeni Admin"/>
```
The global assignment to roles are made in core/workflow/grants.zcml:
```
  <grant permission="bungeni.question.Submit" role="bungeni.Owner"/>
```

The workflow engine can modify the association between permissions and roles on
the contextual object based on status (see **.xml in bungeni/core/workflows/ folder)**


