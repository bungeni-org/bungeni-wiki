

## Schema Changes

Bungeni runs on Postgresql, a relational database. Occasionally due to new requirements or because of a defect fix, the schema of an existing table changes. There are various use cases here which need to be handled with care while upgrading the schema.

## Bungeni Schema

The Bungeni Schema is documented in schema.py as a SQLAlchemy schema (See here [schema.py](http://bungeni-portal.googlecode.com/svn/bungeni.models/trunk/bungeni/models/schema.py) ). Occasionally the schema changes, this can be tracked via the change tracker on googlecode (See here [change tracker](http://code.google.com/p/bungeni-portal/source/list?path=/bungeni.models/trunk/bungeni/models/schema.py).

## Upgrade scripts

An upgrade script needs to be provided when schema.py changes - since schema.py does not handle upgrades by itself. The upgrade script can be a PostgreSQL sql script that alters tables/ alters columns / removes columns etc in a safe manner.

## Use cases for Schema Change

For running any schema change upgrades - the bungeni service must be shutdown to ensure that no user is logged on.

### Schema change on a blank table

If a schema change occurs on a blank table -- it is any easy case since there is no data involved. A alter table statement in postgresql will change the schema. Or alternatively dropping and recreating the table with the new schema should upgrade it.

### Nullable column added on a table with data

This is the case where a new nullable column is added to the table. This is also a relatively easy change. An `alter table add column` statement will add a new column to the table without affecting the data.

### Non nullable column added on a table with data

This is the case where a 'non nullable' column is added to a table with data. This is a problematic issue as the table already has data -- and adding a new 'non nullable' column to the table leads to the question  -- what happens to the data in the new column for existing data rows ? A default will be required for the existing data rows for the new column.

Secondly an `alter table alter column` will not work in this case -- we will first have to create a backup table for the table being upgraded :

```
create table backup_main
select * from main;
```

Then we drop the main table -- but first we would need to delete the constraints.
Then we recreate the main table with the new structure, and we add the constraints back (if the constraints are cascaded -- we will need to re-create all the constraints down the chain). And then we reimport the data from the backup table with the default for the new non-nullable column.

```
insert into main (col1, col2.... new_col)
select col1,col2, .... , 'default value for new col'
from backup_main;
```

In some cases you can use a column default to set a non-null value - and update that to the correct value using a second sql query :
e.g.  this is the bill table schema
```
CREATE TABLE bills
(
  bill_id integer NOT NULL,
  bill_type_id integer NOT NULL,
  ministry_id integer,
  identifier integer,
  publication_date date,
  CONSTRAINT bills_pkey PRIMARY KEY (bill_id),
  CONSTRAINT bills_bill_id_fkey FOREIGN KEY (bill_id)
      REFERENCES parliamentary_items (parliamentary_item_id) MATCH SIMPLE
      ON UPDATE NO ACTION ON DELETE NO ACTION,
  CONSTRAINT bills_bill_type_id_fkey FOREIGN KEY (bill_type_id)
      REFERENCES bill_types (bill_type_id) MATCH SIMPLE
      ON UPDATE NO ACTION ON DELETE NO ACTION,
  CONSTRAINT bills_ministry_id_fkey FOREIGN KEY (ministry_id)
      REFERENCES groups (group_id) MATCH SIMPLE
      ON UPDATE NO ACTION ON DELETE NO ACTION
)
WITH (OIDS=FALSE);
```

if we want to add a non null column called bill\_approval\_date :
```
alter table bills add column bill_approval_date date not null;
```

we get an error message :

```
ERROR:  column "bill_approval_date" contains null values

********** Error **********

ERROR: column "bill_approval_date" contains null values
SQL state: 23502
```

Instead if we run :
```
alter table bills add column bill_approval_date date default now();
```

it successfully adds the column with the current date set as the default, you can now update the column to the correct date using an update query :
```
update bills set bill_approval_date = x.date 
from bills, x
where bills.y = x. z 
.......
```



### An existing column is made non-null for a table with data

The use case is similar to the above, except for the last step where we need a default only for the rows where the existing column appeared as null. So we reload the data with 2 queries.

```
insert into main (col1, col2.... colX)
select col1,col2, .... , 'default value for where the col is null'
from backup_main where colX is null;

insert into main (col1, col2.... colX)
select col1,col2, .... , colX
from backup_main where colX is not null;
```

## Examples with some guidelines

## Schema upgrades across release versions

A documented example is provided here [schema upgrades across revisions](SchemaUpgradesAcrossRevisions.md)


### Adding a new column

TODO

### Converting a 1-to-1 relation ship to a 1-to-many relationship

TODO

### Converting a 1-to-many relationship to a many-to-many relationship

TODO

### Converting a non-nullable field to a nullable field

TODO