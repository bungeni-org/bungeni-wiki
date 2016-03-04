# Validate Input

To avoid user errors validations must be carried out and a feedback to the user is required indicating the error/requirement.


# How to write Validators

# Required Validators

Common requirements for validation in Bungeni are:

## Validate field against db-constraints

### Required Field

A required fields must contain a value.

This is normally handled at database schema level:
```
reporters = rdb.Table(
   "reporters",
   metadata,
   rdb.Column( "reporter_id", rdb.Integer, rdb.ForeignKey('users.user_id'), primary_key=True ),
   rdb.Column( "initials", rdb.Unicode(4), nullable=False ),
   )
```

The field 'initials' will be a required field.

In the descriptor you may define a field as required, this overrides the definition in the schema:
```
class UserDescriptor( ModelDescriptor ):
    fields = [
        dict( name="user_id", omit=True),      
        dict( name="middle_name", label=_(u"Middle Name"), required=True),
        ]
```
or using the property:
```
class UserDescriptor( ModelDescriptor ):
    fields = [
        dict( name="birth_country", 
            property = schema.Choice( title=_(u"Country of Birth"), 
                                       source=DatabaseSource(domain.Country,
                                                            'country_name', 'country_id'),
                                       required=True )
            ),       
        ]

```


### Maximum length of Input

If the database has a restriction e.g. VARCHAR(16) the user has to be warned if the
input does not fit

This is handled at database schema level.

### Check Type of Input

make sure that in a number field you may input only numbers

This is handled at database schema level


### Unique values

give the user a feedback if the value is already present

### Custom validations

If we need a special validation for a certain field (e.g. an email) this can be defined at the descriptor.

```
class UserDescriptor( ModelDescriptor ):
    fields = [
        #dict( name="email", label=_(u"Email")),
        dict( name="email",
            property = schema.TextLine( title =_(u"Email"), 
                                        description=_(u"Email address"),
                                        constraint=check_email,
                                        required=True
                                        ),
             ),                                                                                
        dict( name="login", label=_(u"Login Name")),
        ]
```


The constraint check\_mail is a function that returns True if the input is a valid mail address otherwise an exception will be raised whose message will be displayed to the user.

```
class NotAnEmailAddress(schema.ValidationError):
    """This is not a valid email address"""

def check_email( email ):
    if EMAIL_RE.match( email ) is None:
        raise NotAnEmailAddress(email)
        return False
    return True

EMAIL_RE = "([0-9a-zA-Z_&.+-]+!)*[0-9a-zA-Z_&.+-]+@(([0-9a-zA-Z]([0-9a-zA-Z-]*[0-9a-z-A-Z])?\.)+[a-zA-Z]{2,6}|([0-9]{1,3}\.){3}[0-9]{1,3})$"
```


## Validate one field against another

Validations of this kind can be implemented using invariants.

There are two ways to go about this. One is supply a base interface
with an invariant already defined. The second is an
additional syntax via the descriptor. You can define a sequence of
functions to act as invariants via the descriptor attribute
schema\_invariants. You'll need to raise an invariant exception

For example the date of death must be after the date of birth and if a user is dead he must have the status 'active\_p = 'D''

```
class DeathBeforeLifeError(schema.interfaces.ValidationError):
     """One cannot die before being born"""
    
def DeathBeforeLife(User):
    """Check if date of death is after date of birth"""
    if User.date_of_death is None: return
    if User.date_of_death < User.date_of_birth:
        raise DeathBeforeLifeError
    
def IsDeceased(User):
    """If a user is deceased a date of death must be given"""
    if User.active_p is None: 
        if User.date_of_death is None: 
            return
        else: 
            raise interface.Invalid("If a user is deceased he must have the status 'D'")
    if User.active_p == 'D':
        if User.date_of_death is None:
            raise interface.Invalid("A Date of Death must be given if a user is deceased")
    else:
        if User.date_of_death is not None:
            raise interface.Invalid("If a user is deceased he must have the status 'D'")

```

Add the functions to the descriptor:

```
class UserDescriptor( ModelDescriptor ):
    fields = [
        dict( name="user_id", omit=True),      
        dict( name="date_of_birth", label=_(u"Date of Birth")),       
        dict( name="date_of_death", label=_(u"Date of Death")),
        dict( name="active_p", label=_(u"Active")),        
        ]
        
    schema_invariants = [DeathBeforeLife, IsDeceased]
```



## Access the data of the parent to validate

### field must be filled to constraints of the parent

example: a sitting date must be inside the dates for a session.