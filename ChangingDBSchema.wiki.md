# Introduction
How to add Objects to the DB schema


# Details

## Set up the Schema

The database schema is defined in the file bungeni.core/bungeni/core/schema.py

to add a table you define the table in python like:
```
provinces = rdb.Table(
    "provinces",
    metadata,
    rdb.Column( "province_id", rdb.Integer, primary_key=True ),
    rdb.Column( "province", rdb.Unicode(80), nullable=False ),
    )
```

after you changed the file the db has to be rebuilt.
  * be sure all connections to the db are closed (bin/pg\_ctl stop, bin/pg\_ctl start).
  * run resetdb.
bin/reset\_db

(in my tests this failed sometimes so I had to manually drop the db)
bin/psql bungeni
> drop database

after that reset\_db worked.

have a look at the logs in logs/pg.log and search for the table you created:
```
NOTICE:  CREATE TABLE will create implicit sequence "provinces_province_id_seq" for serial column "provinces.province_id"
NOTICE:  CREATE TABLE / PRIMARY KEY will create implicit index "provinces_pkey" for table "provinces"
```


## Create a Domain Object

In domain.py add an object to the domain model:
```
class Province( object ):
    """
    Province of the Constituency
    """
    pass
```

## Map the Domain Model to the Database Schema

In orm.py add the mapper:
```
mapper( domain.Province, schema.provinces )   
```

## Define the UI in the Descriptor

Add a model descriptor in descriptor.py
```
class ProvinceDescriptor( ModelDescriptor ):
    fields = [
        dict( name="province_id", omit=True ),
        dict( name="province", 
              label=_(u"Province"), 
              description=_(u"Name of the Province"), 
              listing=True ),
        ]
```

## Apply the Generation Components to the domain Model

After your done there, then open up catalyst.zcml and a declaration
like the others, which explicitly applies the generation components
to the domain model using the descriptor. use echo="True" on the
db:catalyst declaration and it will log to the stdout all the things
that its doing.

```
    <db:catalyst
     class=".domain.Province"
     descriptor=".descriptor.ProvinceDescriptor"
     interface_module=".interfaces"
     ui_module="bungeni.ui.content"
     echo="True"
    />
```


## Make the Form available in the Site

so at this point you have the region forms, container, interface
views from the generation.. but its not yet wired into the site
layout/hierarchy.. open app.py.. the app object is basically the root
of the site, there's an appsetup adapter there which setups the
application structure when the app server is started. you can adjust
to add in the region container, in the same manner as the others.

```
        region = domain.RegionContainer()
        self.context['regions'] = region
```