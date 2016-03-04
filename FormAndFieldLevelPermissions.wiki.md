# Form and Field Level Permissions

## Synopsis

Some parliaments may want to selectively disable / hide / change the behaviour of certain fieds on the Form for a content type during setup time.  A UI is required to allow administrator type users to do this configuration.

## Assumptions

Some fields will always be mandatory on the forms (i.e. they cannot be disabled) this is required to preserve data and process integrity - so as to not  break referential integrity of the underlying db, mandatory validations on the forms, and fields used in default workflows.
We need a flag to prevent some fields from being reconfigured via the Field Permissions management UI
(ASHOK: or we can handle it via the documentation root... i.e. document which fields must not be tampered with...)

## Form Level Permissions

Form level permissions are applied to the Form and thus are applicable to the underlying Object. Form level permissions determine at Form level in what mode the Form is available to the current user.

Forms have three kinds of permissions :
  * Add
  * Edit
  * View

Roles can be assigned to these individual permissions, and users associated with the roles are able to receive the Form in the appropriate mode based on these permissions.

e.g.
The following will give Add and Edit permissions to users in the 'Clerks Office' and will allow any user to 'View' the content.
Add = group.clerksOffice (assigned to 'clerks office' user group)
Edit =group.clerksOffice (assigned to 'clerks office' user group)
View = bungeni.public (assigned to anybody)

To make the form visible only to clerks office and to the MP who is the
owner):
Add = group.clerksOffice(assigned to 'clerks office' user group)
Edit = group.clerksOffice (assigned to 'clerks office' user group)
(view): group.clerksOffice, user.Mp.John.Smith (assigned to clerks office group and to the MP who is the owner of the object)

### User Interface

Mockup of the form is displayed with all the fields.
User gets a drag and drop interface to select and assign Add / Edit /View permissions. User can drag and drop user groups and user names.

## Field Level Permissions

Default permissions on fields are View = Bungeni.public (visible to all.)
The permissions mechanism works in the same way as the Form level permissions, except that the Add / Edit / View permissions are set at the field level.

### Assumptions

Form level permissions carry greater weightage than field level permissions.

e.g.  If a user has only 'View' permissions at the Form level and has 'Edit' permissions at the Field level, the Form level 'VIew' permission takes precendence and overrirdes the field level permission.

Field level permission works when there are equivalent permissions at form level.

### User Interface

Same as the User Interface of the Form Level permissions. The same screen lets the user select a field and set the field permissions via drag & drop.


### Example user story

admin wants to change the permissions of Object person, field email
  * default setting is : visible to everybody
  * change to: visible for clerk and speakers office and the MP himself

The UI wil present him a mock up form with all fields and permissions by default fields have the permission bungeni.public,  the email field has the permission (view) bungeni.public => visible for everbody, permission (edit) => editable only  for clerk (assuming that the clerks office has this permission system wide)

So the admin changes the permission (view) to group.Clerks,  and permission (edit) and group.Speaker (edit permission  specially for speakers office assigned systemwide) and  user.MP.John.Smith (systemwide permission to edit the content somebody  owns).