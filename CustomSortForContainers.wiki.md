# Introduction

The default ordering of sqlalchemy is by primary key or OId which is not really helpful for the average human.


# Details

This can be done with a customizations of the container display view.

Specifically you have to customize the listing method which returns a rendered table using the zc.table library. You can specify any sequence of the contained instances that you want, when instantiating the formatter.

E.g. to sort by the query parameter `order_by`:

The `context._class.c._data._list` is a list of all column names in your current context. For security reasons (SQL injection) the parameter is checked against them. Anyway it would not make much sense to order by a column which is not part of the query ;)

```
import alchemist.ui.container
from zope.security import proxy
from zc.table import  table

class ContainerListing( alchemist.ui.container.ContainerListing ):
    
    def update(self):           
        super( ContainerListing, self ).update()
        
    @property
    def formatter( self ):
        context = proxy.removeSecurityProxy( self.context )        
        order_by = self.request.get('order_by', None)       
        query=context._query
        if order_by:
            if order_by in context._class.c._data._list:
                query=query.order_by(order_by)            
            
        formatter = table.AlternatingRowFormatter( context,
                                                   self.request,
                                                   query,
                                                   prefix="form",
                                                   columns = self.columns )
        formatter.cssClasses['table'] = 'listing'
        formatter.table_id = "datacontents"
        return formatter        
```

Register the customized page for your layer:

```
  <!-- Container UI -->
  <browser:page
     for="ore.alchemist.interfaces.IAlchemistContainer"
     permission="zope.View"
     name="index"
     menu="context_actions"
     title="Listing"
     template="templates/generic-container.pt"
     class=".container.ContainerListing"
     layer="bungeni.ui.interfaces.IBungeniSkin"
     />
```