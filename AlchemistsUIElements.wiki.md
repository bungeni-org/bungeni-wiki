# Introduction

A rough guide to tweak the layout of your forms.
Read [ChangingDBSchema](ChangingDBSchema.md) first to understand how the descriptor, schema and mapping work together.


# Add a Combo box to display data from a referenced table

The required imports are:
```
from ore.alchemist.vocabulary import DatabaseSource, QuerySource
```


The Field with the name Province is defined in the schema as:
```
 rdb.Column( "province", rdb.Integer, rdb.ForeignKey('provinces.province_id') ),
```

to get a combobox into the UI use:


```
dict(name="province",
            property = schema.Choice( 
            title=_(u"Province"), 
            source=DatabaseSource(domain.Province,'province', 'province_id'), 
            required=True )
            ),
```

where 'province' is the row to be displayed and 'province\_id' the value to be saved in the field province.

## Display Data from more than one field in the combo box

If the field you need is not present in your table you may add a definition to your object-relational mapper.

e.g. you want to choose full names from parliament members but in your data schema the name is stored in three different fields:
```

users = rdb.Table(
   "users",
   metadata,
   rdb.Column( "user_id", rdb.Integer,  primary_key=True ),
   rdb.Column( "login", rdb.Unicode(16), unique=True, nullable=True ),
   rdb.Column( "titles", rdb.Unicode(32)),
   rdb.Column( "first_name", rdb.Unicode(80), nullable=False ),
   rdb.Column( "last_name", rdb.Unicode(80), nullable=False ),
   rdb.Column( "middle_name", rdb.Unicode(80) ),
   )

```

In the descriptor you query the attribute 'fullname' from the mapped object:
```
class GroupMembershipDescriptor( ModelDescriptor ):

   fields = [       
        dict( name="user_id",
            property=schema.Choice( title=_(u"Member of Parliament"),
                                    source=DatabaseSource(domain.ParliamentMember, 
                                                          'fullname', 'user_id'))
            ),     
        ]
```

The attribute 'fullname' is defined in orm.py as an additional property of ParliamentMember:

```
mapper( domain.ParliamentMember, 
        inherits=domain.User,
          properties={
           'fullname' : column_property(
                             (schema.users.c.first_name + " " + 
                             schema.users.c.middle_name + " " + 
                             schema.users.c.last_name).label('fullname')
                                           )
                    },
        polymorphic_identity='memberofparliament'
      )
```

_Note that if  one of the fields has a NULL value the user\_id will be returned instead!_

QuerySource gives you a more control over the data being retrieved based on your current object. You have to pass additional parameters for the filter (sqlalchemist filter(), SQL: WHERE clause) and ordering (sqlalchemist order\_by(), SQL: ORDER BY clause).
Therefore your domain model **must** not have a filter or order defined!

```
# object that will get passed to QuerySource
class mps_sitting( object ):
    """ returns the mps for a sitting """

# _mp_sitting defines our query that joins the tables together
# DO NOT define any filters only joins here!    
_mp_sitting = rdb.join(schema.sittings, schema.parliament_sessions,
                        schema.sittings.c.session_id == schema.parliament_sessions.c.session_id).join(
                            schema.user_group_memberships,
                            schema.parliament_sessions.c.parliament_id == schema.user_group_memberships.c.group_id).join(
                                schema.users,
                                schema.user_group_memberships.c.user_id == schema.users.c.user_id)

# set up object relational mapping
mapper( mps_sitting, _mp_sitting,
          properties={
           'fullname' : column_property(
                             (schema.users.c.first_name + u" " + 
                             schema.users.c.middle_name + u" " + 
                             schema.users.c.last_name).label('fullname')
                                           )
                    },)
```

Add the your query to the descriptor.

  1. token\_field is the field you want to display to the user (in this case the full name of the parliament member)
  1. value\_field is the value you want to save as the fields value
  1. filter\_field is the name of the field in your query you want to filter by
  1. filter\_value (optional) is the name of the field in your object, the filter will be base on it's value. If you do not pass a value (or None as the value) QuerySource tries to figure out the value by looking up the parents primary key (most common use case)
  1. order\_by\_field (optional) is the name of the field in your query you want to order by
  1. title\_field (optional) is the name of the field ... in your query

```
class AttendanceDescriptor( ModelDescriptor ):
    display_name =_(u"Sitting Attendance")
    membersVocab = QuerySource(vocabulary.mps_sitting, 
                                          token_field='fullname', 
                                          value_field='user_id', 
                                          filter_field='sitting_id', 
                                          filter_value='sitting_id', 
                                          order_by_field='last_name',
                                          title_field='fullname' )                                    
    fields = [
        dict( name="sitting_id", omit=True ),
        dict( name="member_id", listing=True,
                property = schema.Choice(title=_(u"Attendance"), source=membersVocab, ),
               ),       
        ]
```

Some People are more at ease with wit SQL Statements than to create sqlalchemy mappings or if you need a more complicated query it will be easier to formulate this in SQL:

```
    sql_members ='''SELECT "users"."titles" || ' ' || "users"."first_name" || ' ' || "users"."middle_name" || ' ' || "users"."last_name" as user_name, 
                    "users"."user_id", "group_sittings"."sitting_id" 
                    FROM "public"."group_sittings", "public"."sessions", 
                    "public"."user_group_memberships", "public"."users" 
                    WHERE ( "group_sittings"."session_id" = "sessions"."session_id" 
                    AND "user_group_memberships"."group_id" = "sessions"."parliament_id" 
                    AND "user_group_memberships"."user_id" = "users"."user_id" )
                    AND ("group_sittings"."sitting_id" = %(primary_key)s)
                    AND ( "users"."user_id" NOT IN (SELECT member_id 
                                                    FROM sitting_attendance 
                                                    WHERE sitting_id = %(primary_key)s)                                           
                         )
                    ORDER BY "users"."last_name"                    
                    '''
```

and it is added to the descriptor like:
```
    membersVocab = vocabulary.SQLQuerySource(sql_members, 'user_name', 'user_id')                                                                        
    fields = [
        dict( name="sitting_id", omit=True ),
        dict( name="member_id", listing=True,
                property = schema.Choice(title=_(u"Attendance"), source=membersVocab, ),
              listing_column=member_fk_column("member_id", _(u"Member of Parliament") ) ),
        dict( name="attendance_id", listing=True, 
                property = schema.Choice( title=_(u"Attendance"), source=attendanceVocab, required=True),
                listing_column = vocab_column("attendance_id", _(u"Attendance"), attendanceVocab )),            
        ]
```


## Add a column with data from a referenced table to the container listing

To display a value different from the one actually stored in your table/object you pass a listing\_column to your descriptor.

First define how your columns should be rendered:
```
###
# Listing Columns 
# 
def _column( name, title, renderer, default="" ):
    def getter( item, formatter ):
        value = getattr( item, name )
        if value:
            return renderer( value )
        return default
    return column.GetterColumn( title, getter )
    
def day_column( name, title, default="" ):
    renderer = lambda x: x.strftime('%Y-%m-%d')
    return _column( name, title, renderer, default)
        
def vocab_column( name, title, vocabulary_source ):
    def getter( item, formatter ):
        value = getattr( item, name)
        if not value:
            return ''
        formatter_key = "vocabulary_%s"%name
        vocabulary = getattr( formatter, formatter_key, None)
        if vocabulary is None:
            vocabulary = vocabulary_source()
            setattr( formatter, formatter_key, vocabulary)
        term = vocabulary.getTerm( value )
        return term.title or term.token
    return column.GetterColumn( title, getter )

def name_column( name, title, default=""):
    def renderer( value, size=50 ):
        if len(value) > size:
            return "%s..."%value[:size]
        return value
    return _column( name, title, renderer, default)
```

List your dates more user freindly:
```
class GroupDescriptor( ModelDescriptor ):

    fields = [
        dict( name="group_id", omit=True ),
        dict( name="start_date", label=_(u"Start Date"), listing=True, 
              listing_column=day_column("start_date", _(u"Start Date")),),
        dict( name="end_date", label=_(u"End Date"), listing=True, 
              listing_column=day_column('end_date', _(u"End Date")),),                
        ]
```

List the same value you display in your edit combobox in the listing:
```
class AttendanceDescriptor( ModelDescriptor ):
    display_name =_(u"Sitting Attendance")
    attendanceVocab = DatabaseSource(domain.AttendanceType, 'attendance_type', 'attendance_id' )
    membersVocab = QuerySource(vocabulary.mps_sitting, 
                                          token_field='fullname', 
                                          value_field='user_id', 
                                          filter_field='sitting_id', 
                                          filter_value='sitting_id', 
                                          order_by_field='last_name',
                                          title_field='fullname' )                                    
    fields = [
        dict( name="sitting_id", omit=True ),
        dict( name="member_id", listing=True,
                property = schema.Choice(title=_(u"Attendance"), source=membersVocab, ),
              listing_column=vocab_column("member_id", _(u"Member of Parliament"),membersVocab ) ),
        dict( name="attendance_id", listing=True, 
                property = schema.Choice( title=_(u"Attendance"), source=attendanceVocab, required=True),
                listing_column = vocab_column("attendance_id", _(u"Attendance"), attendanceVocab )),            
        ]
```


Display a custom formatted field (full name) in your listing
```
class GroupDescriptor( ModelDescriptor ):

    fields = [
        dict( name="group_id", omit=True ),
        dict( name="short_name", label=_(u"Name"), listing=True),
        dict( name="full_name", label=_(u"Full Name"), listing=True,
              listing_column=name_column("full_name", _(u"Full Name"))),

        ]
```


## Define widgets to edit the object

Simply pass an edit widget to you descriptor to override the build in widgets.

```
   fields = [
        dict( name="start_date", label=_(u"Start Date"), edit_widget=SelectDateWidget ),
        dict( name="end_date", label=_(u"End Date"), edit_widget=SelectDateWidget ),
        dict( name="active_p", label=_(u"Active") ),
```

The same approach is taken for the display widget too.

## Access data of the parent object

## Display data of parent object in the (view/edit) form