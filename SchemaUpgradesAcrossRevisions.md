## Introduction

This page documents a schema upgrade of a running bungeni instance.

In this example -- the bungeni revision r6991 (on Google Code) will be upgrade to r7212 (on Google Code) this involves 10 different schema changes.

## Methodology

We start with the first schema change starting from r6991 (on Google Code) and replay those changes on the data set from before r6991 (on Google Code).

The bungeni schema is recorded in bungeni.models/schema.py - so we use the google code change history of schema.py to create an upgrade script.

This [links provides a chronological list of changes made to the schema](http://code.google.com/p/bungeni-portal/source/list?path=/bungeni.main/trunk/bungeni/models/schema.py&start=7214).

We are interested in all the changes greater than r6991 (on Google Code).

  * [r7003 - was simply a change in formatting of the source file - there were no schema changes](http://code.google.com/p/bungeni-portal/source/detail?r=7003&path=/bungeni.main/trunk/bungeni/models/schema.py)

  * [r7006 - changed the schema of some tables](http://code.google.com/p/bungeni-portal/source/detail?r=7006&path=/bungeni.main/trunk/bungeni/models/schema.py) - the change log links to a couple of issues which document the schema changes - so we can generate an upgrade script for it :
```
ALTER TABLE agenda_item_changes RENAME COLUMN date TO date_audit ;
ALTER TABLE agenda_item_changes ALTER COLUMN date_audit TYPE timestamp without time zone ;
ALTER TABLE agenda_item_changes ADD COLUMN date_active timestamp without time zone not null DEFAULT now() ;
UPDATE agenda_item_changes SET date_active=date_audit ;

ALTER TABLE attached_file_changes RENAME COLUMN date TO date_audit ;
ALTER TABLE attached_file_changes ALTER COLUMN date_audit TYPE timestamp without time zone ;
ALTER TABLE attached_file_changes ADD COLUMN date_active timestamp without time zone not null DEFAULT now() ;
UPDATE attached_file_changes SET date_active=date_audit ;

ALTER TABLE bill_changes RENAME COLUMN date TO date_audit ;
ALTER TABLE bill_changes ALTER COLUMN date_audit TYPE timestamp without time zone ;
ALTER TABLE bill_changes ADD COLUMN date_active timestamp without time zone not null DEFAULT now() ;
UPDATE bill_changes SET date_active=date_audit ;

ALTER TABLE motion_changes RENAME COLUMN date TO date_audit ;
ALTER TABLE motion_changes ALTER COLUMN date_audit TYPE timestamp without time zone ;
ALTER TABLE motion_changes ADD COLUMN date_active timestamp without time zone not null DEFAULT now() ;
UPDATE motion_changes SET date_active=date_audit ;

ALTER TABLE question_changes RENAME COLUMN date TO date_audit ;
ALTER TABLE question_changes ALTER COLUMN date_audit TYPE timestamp without time zone ;
ALTER TABLE question_changes ADD COLUMN date_active timestamp without time zone not null DEFAULT now() ;
UPDATE question_changes SET date_active=date_audit ;

ALTER TABLE tabled_document_changes RENAME COLUMN date TO date_audit ;
ALTER TABLE tabled_document_changes ALTER COLUMN date_audit TYPE timestamp without time zone ;
ALTER TABLE tabled_document_changes ADD COLUMN date_active timestamp without time zone not null DEFAULT now() ;
UPDATE tabled_document_changes SET date_active=date_audit ;

ALTER TABLE agenda_item_changes alter column date_audit set not null;
ALTER TABLE attached_file_changes ALTER COLUMN  date_audit set not null ;
ALTER TABLE bill_changes ALTER COLUMN  date_audit set not null;
ALTER TABLE motion_changes ALTER COLUMN  date_audit set not null;
ALTER TABLE question_changes ALTER COLUMN  date_audit set not null;
ALTER TABLE tabled_document_changes ALTER COLUMN  date_audit set not null;

```

  * [r7031 no significant schema change](http://code.google.com/p/bungeni-portal/source/detail?r=7031&path=/bungeni.main/trunk/bungeni/models/schema.py)

  * [r7109 - the issue log documents the changes](http://code.google.com/p/bungeni-portal/source/detail?r=7109&path=/bungeni.main/trunk/bungeni/models/schema.py) - using that we can generate an upgrade script --
```
ALTER TABLE items_schedule ALTER COLUMN item_id SET not null;
ALTER TABLE items_schedule ALTER COLUMN sitting_id SET not null;
```

  * [r7127 we skip for now](http://code.google.com/p/bungeni-portal/source/detail?r=7127&path=/bungeni.main/trunk/bungeni/models/schema.py) - because the next change reverses the schema change made in this revision.

  * [r7168 has significant changes - but no script is provided in the log](http://code.google.com/p/bungeni-portal/source/detail?r=7168&path=/bungeni.main/trunk/bungeni/models/schema.py) - in this case we look at the [schema diff](http://code.google.com/p/bungeni-portal/source/diff?spec=svn7168&r=7168&format=side&path=/bungeni.main/trunk/bungeni/models/schema.py) to write our own script :
```
ALTER TABLE reports ADD CONSTRAINT report_id_fkey FOREIGN KEY (report_id) REFERENCES parliamentary_items(parliamentary_item_id);
ALTER TABLE reports DROP COLUMN body_text;
ALTER TABLE reports DROP COLUMN note;
ALTER TABLE reports DROP COLUMN language;
ALTER TABLE sitting_reports DROP CONSTRAINT sitting_reports_pkey;
ALTER TABLE sitting_reports DROP COLUMN sitting_report_id;
ALTER TABLE sitting_reports ADD PRIMARY KEY (report_id, sitting_id);

```

  * r7194 (on Google Code) - We again use the [schema diff](http://code.google.com/p/bungeni-portal/source/diff?spec=svn7194&r=7194&format=side&path=/bungeni.main/trunk/bungeni/models/schema.py) to generate the update script :
```
ALTER TABLE reports DROP COLUMN user_id;
ALTER TABLE reports DROP COLUMN created_date;
```

  * r7204 (on Google Code) - the log documents the required changes :
```
ALTER TABLE sessions ALTER COLUMN parliament_id SET not null;
```

  * r7212 (on Google Code) is a more complex change - here, two columns province\_id and region\_id are being moved from the constituencies table into the parliamentary memberships table - so we need to migrate the data in these columns over into the parliamentary memberships table using the constituency\_id as the key.
> First we add the new columns to the parliament\_memberships table --
```
ALTER TABLE parliament_memberships add column province_id integer;
ALTER TABLE parliament_memberships add column region_id integer;
```
> Then we set the value correctly for the new columns using the constituency\_id as the reference --
```
update parliament_memberships  
set province_id  = c.province_id , region_id = c.region_id 
from  constituencies c
where parliament_memberships.constituency_id = c.constituency_id;
```
> Then we remove the migrated columns from the constituencies table --
```
ALTER TABLE constituencies DROP COLUMN province_id;
ALTER TABLE constituencies DROP COLUMN region_id;
```
> Finally we make the newly added columns in the parliament\_memberships table into foreign keys --
```
ALTER TABLE parliament_memberships add foreign key (province_id) references provinces(province_id);
ALTER TABLE parliament_memberships add foreign key (region_id ) references regions(region_id);
```


The composite upgrade script is shown below :
```


--r7006
ALTER TABLE agenda_item_changes RENAME COLUMN date TO date_audit ;
ALTER TABLE agenda_item_changes ALTER COLUMN date_audit TYPE timestamp without time zone ;
ALTER TABLE agenda_item_changes ADD COLUMN date_active timestamp without time zone not null DEFAULT now() ;
UPDATE agenda_item_changes SET date_active=date_audit ;

ALTER TABLE attached_file_changes RENAME COLUMN date TO date_audit ;
ALTER TABLE attached_file_changes ALTER COLUMN date_audit TYPE timestamp without time zone ;
ALTER TABLE attached_file_changes ADD COLUMN date_active timestamp without time zone not null DEFAULT now() ;
UPDATE attached_file_changes SET date_active=date_audit ;

ALTER TABLE bill_changes RENAME COLUMN date TO date_audit ;
ALTER TABLE bill_changes ALTER COLUMN date_audit TYPE timestamp without time zone ;
ALTER TABLE bill_changes ADD COLUMN date_active timestamp without time zone not null DEFAULT now() ;
UPDATE bill_changes SET date_active=date_audit ;

ALTER TABLE motion_changes RENAME COLUMN date TO date_audit ;
ALTER TABLE motion_changes ALTER COLUMN date_audit TYPE timestamp without time zone ;
ALTER TABLE motion_changes ADD COLUMN date_active timestamp without time zone not null DEFAULT now() ;
UPDATE motion_changes SET date_active=date_audit ;

ALTER TABLE question_changes RENAME COLUMN date TO date_audit ;
ALTER TABLE question_changes ALTER COLUMN date_audit TYPE timestamp without time zone ;
ALTER TABLE question_changes ADD COLUMN date_active timestamp without time zone not null DEFAULT now() ;
UPDATE question_changes SET date_active=date_audit ;

ALTER TABLE tabled_document_changes RENAME COLUMN date TO date_audit ;
ALTER TABLE tabled_document_changes ALTER COLUMN date_audit TYPE timestamp without time zone ;
ALTER TABLE tabled_document_changes ADD COLUMN date_active timestamp without time zone not null DEFAULT now() ;
UPDATE tabled_document_changes SET date_active=date_audit ;





ALTER TABLE agenda_item_changes alter column date_audit set not null;
ALTER TABLE attached_file_changes ALTER COLUMN  date_audit set not null ;
ALTER TABLE bill_changes ALTER CO#summary One-sentence summary of this page.

= Introduction =

Add your content here.


= Details =

Add your content here.  Format your content with:
  * Text in *bold* or _italic_
  * Headings, paragraphs, and lists
  * Automatic links to other wiki pages
LUMN  date_audit set not null;
ALTER TABLE motion_changes ALTER COLUMN  date_audit set not null;
ALTER TABLE question_changes ALTER COLUMN  date_audit set not null;
ALTER TABLE tabled_document_changes ALTER COLUMN  date_audit set not null;


--r7109

ALTER TABLE items_schedule ALTER COLUMN item_id SET not null;
ALTER TABLE items_schedule ALTER COLUMN sitting_id SET not null;


--r7127

--skipped

--r7168

ALTER TABLE reports ADD CONSTRAINT report_id_fkey FOREIGN KEY (report_id) REFERENCES parliamentary_items(parliamentary_item_id);
ALTER TABLE reports DROP COLUMN body_text;
ALTER TABLE reports DROP COLUMN note;
ALTER TABLE reports DROP COLUMN language;
ALTER TABLE sitting_reports DROP CONSTRAINT sitting_reports_pkey;
ALTER TABLE sitting_reports DROP COLUMN sitting_report_id;
ALTER TABLE sitting_reports ADD PRIMARY KEY (report_id, sitting_id);


--r7194

ALTER TABLE reports DROP COLUMN user_id;
ALTER TABLE reports DROP COLUMN created_date;


--r7204

ALTER TABLE sessions ALTER COLUMN parliament_id SET not null;

--r7212

ALTER TABLE parliament_memberships add column province_id integer;
ALTER TABLE parliament_memberships add column region_id integer;

update parliament_memberships  
set province_id  = c.province_id , region_id = c.region_id 
from  constituencies c
where parliament_memberships.constituency_id = c.constituency_id;


ALTER TABLE constituencies DROP COLUMN province_id;
ALTER TABLE constituencies DROP COLUMN region_id;

ALTER TABLE parliament_memberships add foreign key (province_id) references provinces(province_id);
ALTER TABLE parliament_memberships add foreign key (region_id ) references regions(region_id);

```