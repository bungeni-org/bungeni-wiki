

## Menus and Actions

General documentation: [From : Zope 3 Web component development](http://books.google.co.uk/books?id=RT7EiQ5ISI4C&pg=PA242)

Menu are definded in zcml as:

```
  <browser:menu
      id="context_actions"
      title="The 'Actions' menu - provides context-specific actions (views)"
      class="ploned.ui.menu.PloneBrowserMenu"
      />
```
To register an action in a menu:
```
   <browser:menuItem
      menu="context_actions"
      for="bungeni.models.interfaces.IMemberOfParliament"
      action="delete"
      title="Delete"
      order="10"
      permission="bungeni.mp.Delete"
      />
```

for Bungeni all registrations are in bungeni/ui/menu.zcml

To use a menu in a zpt template:

```
  <browser:page
      for="ore.alchemist.interfaces.IAlchemistContainer"
      permission="zope.Public"
      name="index"
      menu="context_actions"
      title="Listing"
      template="templates/generic-container.pt"
      class=".table.AjaxContainerListing"
      layer="bungeni.ui.interfaces.IBungeniSkin"
      />
```

with this configuration one can render the menu with:

```
    <tal:block repeat="cview context/@@view_get_menu/context_actions">
```

For viewlets derived from ploned.ui.viewlet.ContentViewsViewlet:

```
    <tal:views repeat="action view/context_actions">
```

because the class ContentViewsViewlet does:
```
   self.context_actions = getMenu("context_actions", self.context, self.request)
```


To generate menu items the code call getMenu then getMenuItems, this one do the
filtering work. In bungeni 'getMenuItems' are defined in bungeni/ui/menu.py, in
this module there are four BrowserMenu re-defining this method, the only one
using workflow is WorkflowMenu that is registered as 'context\_workflow'.


## Workflows

There is no direct relation between forms and workflows.

The relation is between permissions and fields. In bungeni/ui/forms/fields.py there is the method 'filterFields' filtering the fields based on the user permissions on the context (of course the permissions are assigned to roles through the workflows).

The forms using AddForm? and EditForm? (in bungeni/ui/forms/common.py) as base class have an attribute 'form\_fields' results of 'filterFields'. The attribute 'form\_fields' is used in BungeniAttributeDisplay? "setUpWidgets" that is the method called when a form based on zope.formlib library is rendered.