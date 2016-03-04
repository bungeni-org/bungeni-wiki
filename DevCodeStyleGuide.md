# Source Code Style Guide

Recommendations for project source code style,
with the aim of enhancing source code **read**-ability, **grep**-ability and **print**-ability and consequently, _understand_-ability, _debug_-ability, _test_-ability, _maintain_-ability, etc.

However, if conforming to these source style recommendations
reduces any of the above code qualities in any way then conformance
**should not** be adhered to -- use your own good judgement instead.


---



## All Formats

Guidelines that apply to all project source code, irrespective of format e.g.
`.py`, `.xml`, `.zcml`, `.js`, `.css`, etc,
even if focus here is primarily on python and XML.

Only use **editing tools** that do **not automatically modify** source code format in any way! This means you **should not** use any tool that does _any kind of source reformatting_ e.g. auto-reformat whitespace or even change the order of an XML element's attributes. In general, what you want to have is a **good text editor** that respects **your** text source.


### Line length, indentation, continuation lines

  * Limit all lines to a maximum of 79 characters.
  * Use **4 spaces** per _indentation_ and  **1 indentation** per **logical depth level**.
  * Continuation lines should be indented as per their **logical nesting level** i.e. 4 spaces per depth level implied by an open parenthesis, brace, bracket, by an expression continuation or by any other way.
  * The closing _thingy_ (e.g. parenthesis, brace, bracket, tag) of a continuation line should be directly below the beginning of the opening symbol.
  * Only use multiple lines when a single line (of max 79 chars) is not sufficient.
  * Always specify the most _key_ of parameters on the opening line (for long collection-like code segments).


### Quote character

Prefer the double-quote `"` instead of the single-quote `'` character, whenever you have the choice.


### Consistency across languages, order of attributes/parameters

It is very common for a project to make use of more than one language, and in many cases there are references to an object or construct in one language from another e.g. an XML definition of a python construct.

In some languages the order or parameters is (sometimes) relevant e.g. in python, but in other languages such an order is not relevant e.g. attributes in XML. Whenever an equivalent list of attributes or parameters are present (be it as a list of XML attributes, or as the signature of a python callable) then the order should be consistent between the two languages.

For the cases when there is such an order to respect, the order should be clearly commented in appropriate places in the source itself.


### _Revisit_ Comments

Use _Revisit_ comments for code that is temporary, a short-term solution to be reworked, or for code on which there is still questions to be resolved or understood. Multiple related _Revisit_ comments (that use same _label_) may be used across the project source, and across different languages.

Note that authoring a _revisit_ comment is **not** a commitment to revisit it yourself.

A _Revisit_ comments should have a:
  * `!+` : must explicitly start with these 2 characters
  * _label_ : for easier grepping, and to be able to relate multiple comments across the project source (even in different languages)
  * _author identifier_ : so others know who to follow-up with if needed
  * _date_ : so it is easier to judge relevance and status at a later time
  * _description_ : should also indicate what conditions/events would render the comment obsolete.

These pieces of information should be formatted in the following way:
```
    !+LABEL(author, date) description...
```
and should be specified in a comment as per the source format i.e. any of python, XML, doctest text files, HTML templates, css, js, etc.

As example, here are 2 related _revisit_ comments in different files:

`models/interfaces.py`
```
''' !+DATERANGEFILTER(mr, dec-2010) disabled until intention is understood
class IDateRangeFilter(interface.Interface):
    """Adapts a model container instance and a SQLAlchemy query
    object, applies a date range filter and returns a query.

    Parameters: ``start_date``, ``end_date``.

    These must be bound before the query is executed.
    """
'''
```
`models/configure.zcml`
```
    <!-- !+DATERANGEFILTER(mr, dec-2010)
    <adapter for=".domain.Bill"
        provides=".interfaces.IDateRangeFilter"
        factory=".daterange.bill_filter" 
    />
    ...
    -->
```



---


## Python

In addition to style guidelines for _[All Formats](#All_Formats.md)_ above, all Bungeni python source code should also follow additional python-specific style rules.

For any styling issue not explicitly specified here, heed the recommendations
of the following Python Style Guides:

  * [PEP 8 - Style Guide for Python Code](http://www.python.org/dev/peps/pep-0008/) in particular the guidelines concerning _blank lines_, _whitespace_, _comments_, _naming conventions_ and _programming recommendations_.<br />Regarding this one you may find the following tools useful:
    * [pep8 checker tool](http://pypi.python.org/pypi/pep8/)
    * [PythonTidy](http://pypi.python.org/pypi/PythonTidy/)

  * [Google Python Style Guide](http://google-styleguide.googlecode.com/svn/trunk/pyguide.html)<br />In particular, the recommendations pertaining to:
    * [Imports](http://google-styleguide.googlecode.com/svn/trunk/pyguide.html#Imports): _for packages and modules only_.
    * [Packages](http://google-styleguide.googlecode.com/svn/trunk/pyguide.html#Packages): _import each module using the full pathname location of the module_.
    * [Nested/Local/Inner Classes and Functions](http://google-styleguide.googlecode.com/svn/trunk/pyguide.html#Nested/Local/Inner_Classes_and_Functions): _nested/local/inner classes and functions are fine_.
    * [Comments](http://google-styleguide.googlecode.com/svn/trunk/pyguide.html#Comments): _use the right style for module, function, method and in-line comments_.

### Whitespace

Exactly as specified in [PEP 8](http://www.python.org/dev/peps/pep-0008/), but following cases are not explicitly covered there:

Just as for other operators, there should be a space surrounding the % string formatting operator:
```
        assert len(states), "Workflow [%s] defines no states" % (self.name)
```
and not:
```
        assert len(states), "Workflow [%s] defines no states"%(self.name)
```

Contrary to the formatting of brackets and parenthesis for literal lists and tuples, there **should be** a space after the opening and before the closing of list comprehensions and tuple comprehensions (generators):
```
    set([ (p[1], p[2]) for p in states[0].permissions ])
    ( (role, s) for s, p, role in self.permissions if p == permission )
```
and not:
```
    set([(p[1], p[2]) for p in states[0].permissions])
    ((role, s) for s, p, role in self.permissions if p == permission)
```


### Comments and Docstrings

Exactly as specified in [PEP 8](http://www.python.org/dev/peps/pep-0008/), except for following clarifications/refinements:

#### Block and Inline Comments

Python supports single line comments only--anything on a line following an `#` is a comment. Block comments are a sequence of such 1-line comments (with no other code on the line) and are indented as per the code block. Inline comments share the line with some other code.

In both cases, there should be a single space character between the `#` and the comment text i.e.
```
# data dict to be published
data = {}
```
and **not**:
```
#data dict to be published
data = {}
```

If the one-liner comments are just simple indicative phrases, as the sample above, then they should **not be punctuated** e.g. start with a capital or end with a period. But more elaborate comments should **use proper grammar and punctuation**.

#### Commented Code

Contrary to _real_ comments, there should **not** be any additional whitespace characters added after the `#` comment marker i.e.:
```
# data dict to be published
#data = {}
```
and **not**:
```
# data dict to be published
# data = {}
```

But, whenever it is syntacticly possible, the more efficient and clear way to _disable_ (aka to _comment out_) code should be preferred--this is to turn the code into a multi-line string (see _[Quote characters](#Quote_characters.md)_ section), and preferably accompanied with a _[Revisit Comment](#Revisit_Comments.md)_ to indicate why, i.e.:
```
''' !+XYZ(mr, jul-2012) intended for when feature xyz is in place
# data dict to be published
data = {}
'''
```

#### Docstrings

As per [PEP 8](http://www.python.org/dev/peps/pep-0008/), with the recommendation to **not** precede the closing triple double-quote chars `"""` by a blank line, e.g.:
```
def complex(real=0.0, imag=0.0):
    """Form a complex number.
    
    Keyword arguments:
    real -- the real part (default 0.0)
    imag -- the imaginary part (default 0.0)
    """
```

### Quote characters

Always use a single double-quote char `"` to delineate a short literal string.
Only use single-quote `'` when the literal string itself contains **double-quote**
characters that would otherwise require explicit escaping.

Always use triple double-quote chars `"""` to delineate a multi-line string.
Only use triple single-quotes `'''` when the literal string contains itself
**triple double-quote** characters that would otherwise require explicit escaping.

The use of triple single-quotes `'''` should be reserved only to
**temporarily comment out** entire code sections.

For a long string that should not include any additional whitespace as a result
of pretty source formatting the auto-concatenation feature of
adjacent strings should be used e.g. (preferably):
```
    long_string = "some initial text as part of a single long line string " \
        "and some more text for the same string and some more text for the " \
        "same string"
```
or:
```
    long_string = ("some initial text as part of a single long line string "
        "and some more text for the same string and some more text for the "
        "same string")
```
but not:
```
    long_string = ("some initial text as part of a single long line string " +
        "and some more text for the same string and some more text for the " +
        "same string")
```

#### Quoting _inlined source code_ strings

Always delineate embedded code with triple double-quote chars `"""` -- even if the string is not multi-line. Example, here's a snippet of inlined SQL:
```
        rdb.CheckConstraint("""active_p in ('A', 'I', 'D')"""),
```

### Multi-line parameter lists, literal lists, objects, block statements, expressions

The exact same _[Line length, indentation, continuation lines](#Line_length,_indentation,_continuation_lines.md)_ style rules for all formats, above, are the rules to apply here, with the _one essential rule_ to always remember being **one indentation level per logical nesting level**.

Some examples:

```
    columns = [
        rdb.Column("version_id", rdb.Integer, primary_key=True),
        rdb.Column("content_id", rdb.Integer, rdb.ForeignKey(table.c[fk_id])),
        rdb.Column("change_id", rdb.Integer, 
            rdb.ForeignKey("%s_changes.change_id" % entity_name)
        ),
        rdb.Column("manual", rdb.Boolean, nullable=False, default=False),
    ]
```

```
mapper(domain.User, schema.users,
    properties={"user_addresses": relation(domain.UserAddress)}
)
```

```
mapper(domain.QuestionVersion, schema.question_versions,
    properties={
        "change": relation(domain.QuestionChange, uselist=False),
        "head": relation(domain.Question, uselist=False),
        "attached_files": relation(domain.AttachedFileVersion,
            primaryjoin=rdb.and_(
                schema.question_versions.c.content_id ==
                    schema.attached_file_versions.c.item_id,
                schema.question_versions.c.version_id ==
                    schema.attached_file_versions.c.file_version_id
            ),
            foreign_keys=[schema.attached_file_versions.c.item_id,
                schema.attached_file_versions.c.file_version_id
            ]
        ),
    }
)
```

Same rules apply for **multi-line block-opening statements**, giving the following results (with block opener implying 1 nesting level and the parameter continuation lines implying another):

```
    def __init__(self,
            # resolvable python callable
            condition="owner_receive_notification",
            # i18n, template source
            subject="${item.class_name} ${item.status}: ${item.short_name}", 
            # template source 
            from_="${site.clerk_email}",
            # template source 
            to="${item.owner_email}",
            # i18n, template source
            body="${item.class_name} ${item.status}: ${item.short_name}",
            # documentational note
            note=None
        ):
        self.condition = capi.get_workflow_condition(condition)
        ...
```

```
    if (IAlchemistContent.providedBy(context) and
            IDCDescriptiveProperties.providedBy(context) and
            ILocation.providedBy(context)
        ):
        title = context.title
```

Chaining of expressions can sometimes result in code that is very awkward to format in a readable way. The recommendation here is to first limit to a _single but complete_ sub-expression per line i.e. each 1-line sub-expression may itself be composite as long as it fits in the line. Then, to mark the "closing-chained-continuation" just add one additional indentation (relative to the statement/expression opening) with the closing parenthesis of each chained sub-expression on the next line as start of the next chained expression.

For example, the `session.query(...)` below is an expression that does not fit on a single line, while the `.filter(...)`, `.order_by(...)` and `.options(...)` are single expressions that do:

```
        query = session.query(domain.Sitting
            ).filter(self.sitting_filter
            ).order_by(schema.sitting.c.start_date
            ).options(eagerload("group"), eagerload("item_schedule"))
```

The same above code should **not** be formatted something like:

```
        query = session.query(domain.Sitting).filter(self.sitting_filter
            ).order_by(schema.sitting.c.start_date).options(
                eagerload("group"), eagerload("item_schedule"))
```


<a href='Hidden comment: 
indentation is not quite as other cases, as in theory each closing paren
could *end* the total expression, except that we "chain on"...

So, as rule for "close-continuations" is to add an indentation, then all else
should also follow suit, even any "long or multi parameters" of individual
sub-expressions:

member_of_parliament = rdb.join(schema.user_group_memberships,
schema.users,
schema.user_group_memberships.c.user_id == schema.users.c.user_id
).join(schema.parliaments,
schema.user_group_memberships.c.group_id ==
schema.parliaments.c.parliament_id)
mapper(MemberOfParliament, member_of_parliament)
'></a>


### Multiple forms for same -- adopt and use one

#### Comparison operators

Always use `!=` and not `<>` (that is equivalent).


---


## XML


### Blank lines

If you must, use blank lines **sparingly** -- only use a single blank line between elements on the infrequent occasion when those elements are completely unrelated to each other.


### Multi-line elements

Again, the same _[Line length, indentation, continuation lines](#Line_length,_indentation,_continuation_lines.md)_ style rules for all formats, above, are the rules to apply here.

Non-empty XML elements should have the closing tag:
  * either on same single line as the opening tag
  * or at exactly same indent as opening tag

Same goes for the ` />` ending of empty XML elements.

There **should not be** any whitespace preceeding the closing `>` of a tag; but, to enhance the distinction between empty and non-empty elements, there **should be** a single space preceeding the ` />` ending of empty XML elements.

Examples:
```
    <class class=".Ministry"><require like_class=".Group" /></class>
```
```
    <class class="bungeni.models.domain.Ministry">
        <require like_class="bungeni.models.domain.Group" />
    </class>
```
```
    <browser:menu id="admin_navigation" title="Actions Admin" />
    <browser:menuItem menu="admin_navigation"
        for="bungeni.models.interfaces.IBungeniApplication"
        title="Application Settings"
        action="settings"
        layer=".interfaces.IAdministratorWorkspace"
        permission="zope.ManageSite"
    />
```

When an element has a clear _key_ attribute --
i.e. the one most obvious attribute by which the element could be identified
or grouped e.g. the `id` or `name` attribute, the `menu` attribute
for `<menuItem>` elements, etc. --
it should be specified first and on same line as opening tag.
For example:
```
    <adapter factory=".workspace.WorkspaceContainerTraverser"
        permission="zope.Public"
        trusted="true"
    />
```


---


## Relational Database

  * Table names are singular.
  * Table and column names should be in lowercase and composed of underscore-separated whole words.
  * For the cases when a table has a single auto-generated surrogate PK column the name of the column should be: `"%s_id" % (table_name)`.
  * Index names should be: `"%s_idx_%s" % (table_name, "_".join(column_names))`, where the ORDER of column names should follow the order of definition in the table. Plus, should there ever be the need to have multiple indices on same column set, an additional descriptive postfix e.g. `_unique`.
  * Prefix boolean-type columns with `is_` or `has_`.
  * Suffix both date and datetime type columns with `_date` !+?


<a href='Hidden comment: 
'></a>
