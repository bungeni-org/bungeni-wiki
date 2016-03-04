!+OUTOFDATE(mr, jun-2011)

The viewlet managers are:

  * version.manager in ui/version.zcml based on .version.VersionViewletManager
  * bungeni.subform.manager in ui/forms/configure.zcml base on viewlets.SubFormViewletManager
  * bungeni.scheduling in ui/viewlets/schedule.zcml with no declared class
  * bungeni.workspace in ui/viewlets/workspace.zcml based on zope.viewlet.manager.WeightOrderedViewletManager
  * bungeni.workspace-archive in ui/viewlets/workspace.zcml based on zope.viewlet.manager.WeightOrderedViewletManager

For 'version.manager' there is no 'version.py' module and it does not
include (the now deleted, circa r8360 (on Google Code)) 'version.zcml' in any zcml file.

'bungeni.scheduling', 'bungeni.workspace, 'bungeni.workspace-archive' are based
on standard viewlet manager.

The only manager doing filtering on viewlets is 'SubFormViewletManager' in
bungeni/ui/forms/viewlets.py
The class has a 'filter' method calling 'for\_display' on viewlets, but the only
viewlet using the workflow is SupplementaryQuestionsViewlet, the code is:

```
    @property
    def for_display(self):
        return self.context.__parent__.status == question_wf_state[u"response_submitted"].id
```
