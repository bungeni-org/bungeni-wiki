

# Introduction
An office in bungeni is a group of users who have some role specific functionality. e.g. the members of the "clerk's office" get the functionality available with the "clerk" role in the system

# Objective

To add a new office specifically for handling questions -- "Questions Office"

# Steps

## 1)  Add support for new "type of office"

### a)  bungeni.models/schema.py updated to support a new "type of office" ('Q' - for questions )

```
Index: schema.py
===================================================================
--- schema.py	(revision 7561)
+++ schema.py	(working copy)
@@ -256,7 +256,7 @@
     # Speakers office or Clerks office, the members of members of
     # this group will get local roles in the parliament accordingly
     rdb.Column("office_type", rdb.String(1),
-        rdb.CheckConstraint("""office_type in ('S','C', 'T','L','R')"""),
+        rdb.CheckConstraint("""office_type in ('S','C', 'T','L','R', 'Q')"""),
         nullable=False
     ),
 )

```

### b) bungeni.ui/vocabulary.py updated to support a 'Question Office' vocabulary item

```
Index: vocabulary.py
===================================================================
--- vocabulary.py	(revision 7561)
+++ vocabulary.py	(working copy)
@@ -106,6 +106,9 @@
     vocabulary.SimpleTerm('L', _(u"Library Office"), _(u"Library Office")),
     vocabulary.SimpleTerm('R', _(u"Researcher Office"), 
         _(u"Researcher Office")),
+    vocabulary.SimpleTerm('Q', _(u"Question Office"), 
+        _(u"Question Office")),
+
 ])
 YesNoSource = vocabulary.SimpleVocabulary([
     vocabulary.SimpleTerm(True, _(u"Yes"), _(u"Yes")), 

```



## 2) Add a new role definition to support the new office type

in bungeni.models/roles.zcml

```
Index: roles.zcml
===================================================================
--- roles.zcml	(revision 7561)
+++ roles.zcml	(working copy)
@@ -11,6 +11,7 @@
   <role id="bungeni.Owner" title="Owner" />
   <role id="bungeni.Minister" title="Minister" />
   <role id="bungeni.Clerk" title="Clerks Office" />
+  <role id="bungeni.QuestionClerk" title="Questions Office" />
   <role id="bungeni.Speaker" title="Speaker Office" />
   <role id="bungeni.Translator" title="Translators  Office" />
   <role id="bungeni.Everybody" title="All authenticated users" />
```

## 3) Add required default permissions to security.zcml for the new role
We grant only question related permissions to the new role

```
Index: security.zcml
===================================================================
--- security.zcml	(revision 7561)
+++ security.zcml	(working copy)
@@ -38,6 +38,7 @@
 
   <grant permission="zope.View" role="bungeni.MP" />
   <grant permission="zope.View" role="bungeni.Clerk" />
+  <grant permission="zope.View" role="bungeni.QuestionClerk" />
   <grant permission="zope.View" role="bungeni.Speaker" />
   <grant permission="zope.View" role="bungeni.Owner" />
   <grant permission="zope.View" role="bungeni.Everybody" />
@@ -81,6 +82,7 @@
   
   <grant permission="bungeni.question.Add" role="bungeni.MP" />
   <grant permission="bungeni.question.Add" role="bungeni.Clerk" />
+  <grant permission="bungeni.question.Add" role="bungeni.QuestionClerk" />
   <grant permission="bungeni.question.Add" role="bungeni.Speaker" />
   <grant permission="bungeni.question.Edit" role="bungeni.Owner" />
   <grant permission="bungeni.question.Delete" role="bungeni.Owner" />
```


## 4) Modify the  `_get_group_local_role()` API in bungeni.core.workflows.utils to support mapping the newly defined role to the "Questions Office"

```
Index: utils.py
===================================================================
--- utils.py	(revision 7561)
+++ utils.py	(working copy)
@@ -76,6 +76,8 @@
             return "bungeni.Clerk"
         elif group.office_type == "T":
             return "bungeni.Translator"
+        elif group.office_type == "Q":
+            return "bungeni.QuestionClerk"
         else: 
             raise NotImplementedError 
     else:
```

## 5) Modify the workflow defaults grants in bungeni.core.workflows/grants.zcml with grants for the bungeni.QuestionClerk role (only for question related permissions)

```
Index: grants.zcml
===================================================================
--- grants.zcml	(revision 7561)
+++ grants.zcml	(working copy)
@@ -6,26 +6,42 @@
   <grant permission="bungeni.question.Submit" role="bungeni.Owner"/>
   <grant permission="bungeni.question.Receive" role="bungeni.Clerk"/>
   <grant permission="bungeni.question.clerk.Review" role="bungeni.Clerk"/>
+  <grant permission="bungeni.question.Receive" role="bungeni.QuestionClerk"/>
+  <grant permission="bungeni.question.clerk.Review" role="bungeni.QuestionClerk"/>
+
   <grant permission="bungeni.question.speaker.Review"  role="bungeni.Speaker"/>
   <grant permission="bungeni.question.Schedule" role="bungeni.Clerk"/>
   <grant permission="bungeni.question.Respond" role="bungeni.Clerk"/>
   <grant permission="bungeni.question.Answer" role="bungeni.Clerk"/>
+  <grant permission="bungeni.question.Schedule" role="bungeni.QuestionClerk"/>
+  <grant permission="bungeni.question.Respond" role="bungeni.QuestionClerk"/>
+  <grant permission="bungeni.question.Answer" role="bungeni.QuestionClerk"/>
+
     <grant permission="bungeni.question.Withdraw" role="bungeni.Owner"/>
     <grant permission="bungeni.question.Withdraw" role="bungeni.Clerk"/>
+    <grant permission="bungeni.question.Withdraw" role="bungeni.QuestionClerk"/>
+
   <grant permission="bungeni.question.Write_answer" role="bungeni.Minister" />
   <grant permission="bungeni.question.Elapse" role="bungeni.Clerk"/>
+  <grant permission="bungeni.question.Elapse" role="bungeni.QuestionClerk"/>
+
   <grant permission="bungeni.question.Elapse" role="bungeni.Speaker"/>
   <!-- response workflow permissions -->
   <grant permission="bungeni.response.Submit" role="bungeni.Minister"/>
   <grant permission="bungeni.response.Complete" role="bungeni.Clerk"/>
     <grant permission="bungeni.response.RevertTransitions" role="bungeni.Clerk" />
+  <grant permission="bungeni.response.Complete" role="bungeni.QuestionClerk"/>
+    <grant permission="bungeni.response.RevertTransitions" role="bungeni.QuestionClerk" />
+
   <!--TODO motion workflow permissions -->
   <grant permission="bungeni.motion.Submit" role="bungeni.Owner"/>
   <grant permission="bungeni.motion.Receive" role="bungeni.Clerk"/>
   <grant permission="bungeni.motion.clerk.Review" role="bungeni.Clerk"/>
+
   <grant permission="bungeni.motion.speaker.Review"  role="bungeni.Speaker"/>
   <grant permission="bungeni.motion.Schedule" role="bungeni.Clerk"/>
   <grant permission="bungeni.motion.Debate" role="bungeni.Clerk"/>
+
   <grant permission="bungeni.motion.Debate" role="bungeni.Speaker"/>
     <grant permission="bungeni.motion.Withdraw" role="bungeni.Owner"/>
     <grant permission="bungeni.motion.Withdraw" role="bungeni.Clerk"/>
@@ -83,9 +99,13 @@
           
   <!-- file attachments -->
   <grant permission="bungeni.attachedfile.Deactivate" role="bungeni.Clerk"/>
+  <grant permission="bungeni.attachedfile.Deactivate" role="bungeni.QuestionClerk"/>
+
   <grant permission="bungeni.attachedfile.Deactivate" role="bungeni.Speaker"/>
```


## 6) Modify the questions workflow by adding grants for the bungeni.QuestionClerk role

```
Index: question.xml
===================================================================
--- question.xml	(revision 7561)
+++ question.xml	(working copy)
@@ -18,12 +18,17 @@
     
     <state id="working_draft" title="Working Draft">
         <grant permission="bungeni.question.Edit" role="bungeni.Clerk" />
+        <grant permission="bungeni.question.Edit" role="bungeni.QuestionClerk" />
+
         <grant permission="bungeni.question.Edit" role="bungeni.Speaker" />
         <grant permission="bungeni.question.Delete" role="bungeni.Clerk" />
+        <grant permission="bungeni.question.Delete" role="bungeni.QuestionClerk" />
         <grant permission="bungeni.question.Delete" role="bungeni.Speaker" />
         <grant permission="zope.View" role="bungeni.Clerk" />
+        <grant permission="zope.View" role="bungeni.QuestionClerk" />
         <grant permission="zope.View" role="bungeni.Speaker" />
         <grant permission="bungeni.fileattachment.Add" role="bungeni.Clerk" />
+        <grant permission="bungeni.fileattachment.Add" role="bungeni.QuestionClerk" />
         <grant permission="bungeni.fileattachment.Edit" role="bungeni.Speaker" />
         <deny permission="zope.View" role="bungeni.Owner" />
         <deny permission="bungeni.question.Add" role="bungeni.MP" />
@@ -31,6 +36,10 @@
         <deny permission="zope.View" role="bungeni.Minister" />
         <deny permission="zope.View" role="bungeni.Everybody" /> 
         <deny permission="zope.View" role="bungeni.Anybody" />
+        <deny permission="question.note.View" role="bungeni.MP" />
+        <deny permission="question.note.Edit" role="bungeni.MP" />
+        <deny permission="question.note.Add" role="bungeni.MP" />
+
     </state>
     <state id="draft" title="Draft">
         <grant permission="bungeni.question.Edit" role="bungeni.Owner" />
@@ -39,36 +48,58 @@
         <deny permission="bungeni.question.Add" role="bungeni.MP" />
         <deny permission="zope.View" role="bungeni.MP" />
         <deny permission="zope.View" role="bungeni.Clerk" />
+        <deny permission="zope.View" role="bungeni.QuestionClerk" />
         <deny permission="zope.View" role="bungeni.Speaker" />
         <deny permission="zope.View" role="bungeni.Minister" />
         <deny permission="zope.View" role="bungeni.Everybody" /> 
         <deny permission="zope.View" role="bungeni.Anybody" />
         <grant permission="bungeni.fileattachment.Add" role="bungeni.Owner" />
         <grant permission="bungeni.fileattachment.Edit" role="bungeni.Owner" />
+        <deny permission="question.note.View" role="bungeni.MP" />
+        <deny permission="question.note.Edit" role="bungeni.MP" />
+        <deny permission="question.note.Add" role="bungeni.MP" />
     </state>
     <state id="submitted" title="Submitted to clerk">
         <deny permission="bungeni.question.Edit" role="bungeni.Owner" />
         <grant permission="zope.View" role="bungeni.Owner" />
         <grant permission="zope.View" role="bungeni.Clerk" />
+        <grant permission="zope.View" role="bungeni.QuestionClerk" />
         <grant permission="zope.View" role="bungeni.Speaker" />
         <deny permission="bungeni.question.Delete" role="bungeni.Owner" />
         <deny permission="bungeni.question.Delete" role="bungeni.Clerk" />
+        <deny permission="bungeni.question.Delete" role="bungeni.QuestionClerk" />
+
         <deny permission="bungeni.question.Delete" role="bungeni.Speaker" />
         <deny permission="bungeni.fileattachment.Add" role="bungeni.Owner" />
         <deny permission="bungeni.fileattachment.Edit" role="bungeni.Owner" />
+        <deny permission="question.note.View" role="bungeni.Clerk" />
+        <deny permission="question.note.Edit" role="bungeni.Clerk" />
+        <deny permission="question.note.Add" role="bungeni.Clerk" />
+        <deny permission="question.note.View" role="bungeni.QuestionClerk" />
+        <deny permission="question.note.Edit" role="bungeni.QuestionClerk" />
+        <deny permission="question.note.Add" role="bungeni.QuestionClerk" />
+
     </state>
     <state id="received" title="Received by clerk">
         <grant permission="bungeni.question.Edit" role="bungeni.Clerk" />
         <grant permission="bungeni.fileattachment.Add" role="bungeni.Clerk" />
         <grant permission="bungeni.fileattachment.Edit" role="bungeni.Clerk" />
+        <grant permission="bungeni.question.Edit" role="bungeni.QuestionClerk" />
+        <grant permission="bungeni.fileattachment.Add" role="bungeni.QuestionClerk" />
+        <grant permission="bungeni.fileattachment.Edit" role="bungeni.QuestionClerk" />
+
     </state>
     <state id="complete" title="Submitted to the Speaker">
         <deny permission="bungeni.question.Edit" role="bungeni.Clerk" />
+        <deny permission="bungeni.question.Edit" role="bungeni.QuestionClerk" />
         <grant permission="bungeni.question.Edit" role="bungeni.Speaker" />
         <grant permission="zope.View" role="bungeni.Speaker" />
         <deny permission="bungeni.fileattachment.Add" role="bungeni.Clerk" />
+        <deny permission="bungeni.fileattachment.Add" role="bungeni.QuestionClerk" />
+
         <grant permission="bungeni.fileattachment.Add" role="bungeni.Speaker" /> 
         <deny permission="bungeni.fileattachment.Edit" role="bungeni.Clerk" />
+        <deny permission="bungeni.fileattachment.Edit" role="bungeni.QuestionClerk" />
         <grant permission="bungeni.fileattachment.Edit" role="bungeni.Speaker" />
     </state>
     <state id="admissible" title="Admissible">
@@ -85,19 +116,32 @@
     <state id="clarify_mp" title="Needs MPs clarification">
         <grant permission="zope.View" role="bungeni.Owner" />
         <deny permission="bungeni.question.Edit" role="bungeni.Clerk" />
+        <deny permission="bungeni.question.Edit" role="bungeni.QuestionClerk" />
+
         <grant permission="bungeni.question.Edit" role="bungeni.Owner" />
-        <deny permission="bungeni.fileattachment.Add" role="bungeni.clerk" />
+        <deny permission="bungeni.fileattachment.Add" role="bungeni.Clerk" />
+        <deny permission="bungeni.fileattachment.Add" role="bungeni.QuestionClerk" />
         <grant permission="bungeni.fileattachment.Add" role="bungeni.Owner" />
-        <deny permission="bungeni.fileattachment.Edit" role="bungeni.clerk" />
+        <deny permission="bungeni.fileattachment.Edit" role="bungeni.Clerk" />
+        <deny permission="bungeni.fileattachment.Edit" role="bungeni.QuestionClerk" />
+
         <grant permission="bungeni.fileattachment.Edit" role="bungeni.Owner" />
+        <grant permission="question.note.View" role="bungeni.Owner" />
+        <grant permission="question.note.Edit" role="bungeni.MP" />
+
     </state>
     <state id="clarify_clerk" title="Needs clerks clarification">
         <deny permission="bungeni.question.Edit" role="bungeni.Speaker" />
         <grant permission="bungeni.question.Edit" role="bungeni.Clerk" />
+        <grant permission="bungeni.question.Edit" role="bungeni.QuestionClerk" />
         <deny permission="bungeni.fileattachment.Add" role="bungeni.Speaker" />
         <grant permission="bungeni.fileattachment.Add" role="bungeni.Clerk" /> 
+        <grant permission="bungeni.fileattachment.Add" role="bungeni.QuestionClerk" /> 
+
         <deny permission="bungeni.fileattachment.Edit" role="bungeni.Speaker" />
         <grant permission="bungeni.fileattachment.Edit" role="bungeni.Clerk" />
+        <grant permission="bungeni.fileattachment.Edit" role="bungeni.QuestionClerk" />
+
     </state>
     <state id="schedule_pending" title="Schedule pending">
         <deny permission="bungeni.question.Edit" role="bungeni.Speaker" />
@@ -105,7 +149,11 @@
     <state id="scheduled" title="Scheduled">
         <deny permission="bungeni.question.Edit" role="bungeni.Speaker" />
         <grant permission="bungeni.response.Add" role="bungeni.Clerk" />
+        <grant permission="bungeni.response.Add" role="bungeni.QuestionClerk" />
+
         <grant permission="bungeni.response.View" role="bungeni.Clerk" />
+        <grant permission="bungeni.response.Add" role="bungeni.QuestionClerk" />
+
     </state>
     <state id="debate_adjourned" title="Debate adjourned">
     </state>
@@ -117,16 +165,28 @@
         <deny permission="bungeni.question.Edit" role="bungeni.Clerk" />
         <deny permission="bungeni.fileattachment.Add" role="bungeni.Clerk" /> 
         <deny permission="bungeni.fileattachment.Edit" role="bungeni.Clerk" />
+        <deny permission="bungeni.question.Edit" role="bungeni.QuestionClerk" />
+        <deny permission="bungeni.fileattachment.Add" role="bungeni.QuestionClerk" /> 
+        <deny permission="bungeni.fileattachment.Edit" role="bungeni.QuestionClerk" />
+
     </state>
     <state id="response_submitted" title="Response submitted">
         <grant permission="zope.View" role="bungeni.Clerk"/>
+        <grant permission="zope.View" role="bungeni.QuestionClerk"/>
+
         <deny permission="bungeni.response.Edit" role="bungeni.Minister"/>
         <grant permission="bungeni.response.View" role="bungeni.Clerk" />
+        <grant permission="bungeni.response.View" role="bungeni.QuestionClerk" />
+
         <grant permission="bungeni.response.View" role="bungeni.Speaker" />
         <!-- to enable edit, thus enabling follow-up via redraft_response -->
         <grant permission="bungeni.question.Edit" role="bungeni.Clerk" />
         <grant permission="bungeni.fileattachment.Add" role="bungeni.Clerk" /> 
         <grant permission="bungeni.fileattachment.Edit" role="bungeni.Clerk" />
+        <grant permission="bungeni.question.Edit" role="bungeni.QuestionClerk" />
+        <grant permission="bungeni.fileattachment.Add" role="bungeni.QuestionClerk" /> 
+        <grant permission="bungeni.fileattachment.Edit" role="bungeni.QuestionClerk" />
+
     </state>
     <state id="response_complete" title="Response completed">
         <grant permission="zope.View" role="bungeni.Everybody" />
@@ -140,39 +200,57 @@
     </state>
     <state id="debated" title="Debated">
         <deny permission="bungeni.question.Edit" role="bungeni.Clerk" />
+        <deny permission="bungeni.question.Edit" role="bungeni.QuestionClerk" />
+
         <deny permission="bungeni.question.Edit" role="bungeni.Speaker" />
     </state>
     <state id="dropped" title="Dropped">
         <deny permission="bungeni.question.Edit" role="bungeni.Speaker" />
         <deny permission="bungeni.question.Edit" role="bungeni.Clerk" />
+        <deny permission="bungeni.question.Edit" role="bungeni.QuestionClerk" />
         <deny permission="bungeni.question.Edit" role="bungeni.Owner" />
         <deny permission="bungeni.fileattachment.Add" role="bungeni.Speaker" />
         <deny permission="bungeni.fileattachment.Add" role="bungeni.Clerk" />
+        <deny permission="bungeni.fileattachment.Add" role="bungeni.QuestionClerk" />
         <deny permission="bungeni.fileattachment.Add" role="bungeni.Owner" />
         <deny permission="bungeni.fileattachment.Edit" role="bungeni.Speaker" />
         <deny permission="bungeni.fileattachment.Edit" role="bungeni.Clerk" />
+        <deny permission="bungeni.fileattachment.Edit" role="bungeni.QuestionClerk" />
+
         <deny permission="bungeni.fileattachment.Edit" role="bungeni.Owner" />
     </state>
     <state id="withdrawn" title="Withdrawn">
         <deny permission="bungeni.question.Edit" role="bungeni.Speaker" />
         <deny permission="bungeni.question.Edit" role="bungeni.Clerk" />
+        <deny permission="bungeni.question.Edit" role="bungeni.QuestionClerk" />
+
         <deny permission="bungeni.question.Edit" role="bungeni.Owner" />
         <deny permission="bungeni.fileattachment.Add" role="bungeni.Speaker" />
         <deny permission="bungeni.fileattachment.Add" role="bungeni.Clerk" />
+        <deny permission="bungeni.fileattachment.Add" role="bungeni.QuestionClerk" />
+
         <deny permission="bungeni.fileattachment.Add" role="bungeni.Owner" />
         <deny permission="bungeni.fileattachment.Edit" role="bungeni.Speaker" />
         <deny permission="bungeni.fileattachment.Edit" role="bungeni.Clerk" />
+        <deny permission="bungeni.fileattachment.Edit" role="bungeni.QuestionClerk" />
+
         <deny permission="bungeni.fileattachment.Edit" role="bungeni.Owner" />
     </state>
     <state id="withdrawn_public" title="Withdrawn">
         <deny permission="bungeni.question.Edit" role="bungeni.Speaker" />
         <deny permission="bungeni.question.Edit" role="bungeni.Clerk" />
+        <deny permission="bungeni.question.Edit" role="bungeni.QuestionClerk" />
+
         <deny permission="bungeni.question.Edit" role="bungeni.Owner" />
         <deny permission="bungeni.fileattachment.Add" role="bungeni.Speaker" />
         <deny permission="bungeni.fileattachment.Add" role="bungeni.Clerk" />
+        <deny permission="bungeni.fileattachment.Add" role="bungeni.QuestionClerk" />
+
         <deny permission="bungeni.fileattachment.Add" role="bungeni.Owner" />
         <deny permission="bungeni.fileattachment.Edit" role="bungeni.Speaker" />
         <deny permission="bungeni.fileattachment.Edit" role="bungeni.Clerk" />
+        <deny permission="bungeni.fileattachment.Edit" role="bungeni.QuestionClerk" />
+
         <deny permission="bungeni.fileattachment.Edit" role="bungeni.Owner" />
     </state>
```

## 7) Modify the attachedfile workflow by adding grants for the bungeni.QuestionClerk role

```
Index: attachedfile.xml
===================================================================
--- attachedfile.xml	(revision 7561)
+++ attachedfile.xml	(working copy)
@@ -9,6 +9,7 @@
   <state id="inactive" title="Inactive file obsolete">
     <deny permission="zope.View" role="bungeni.Owner" />
     <grant permission="zope.View" role="bungeni.Clerk" />
+    <grant permission="zope.View" role="bungeni.QuestionClerk" />
     <grant permission="zope.View" role="bungeni.Speaker" />
     <deny permission="zope.View" role="bungeni.MP" />
     <deny permission="zope.View" role="bungeni.Minister" />
```



## 8) The member of the Questions Office requires a specific workspace, so we need to make the required definitions

We add a view interface called IQuestionClerkWorkspace :
```
Index: interfaces.py
===================================================================
--- interfaces.py	(revision 7561)
+++ interfaces.py	(working copy)
@@ -37,6 +37,8 @@
     """Speaker's workspace."""
 class IClerkWorkspace(IBrowserView):
     """Clerk's workspace."""
+class IQuestionClerkWorkspace(IBrowserView):
+    """Question Clerk's workspace."""
 class IAdministratorWorkspace(IBrowserView): # !+ remove out
     """Administrator's workspace."""
 class IMinisterWorkspace(IBrowserView):
```

## 9) We have to modify the prepare\_user\_workspaces() API in bungeni.ui/workspace.py
> to process the workspace for the "bungeni.QuestionClerk" role and also the WorkspaceSectionView.role\_interface\_mapping to add a role->interface mapping for the newly added interface.

```
Index: workspace.py
===================================================================
--- workspace.py	(revision 7561)
+++ workspace.py	(working copy)
@@ -109,9 +109,10 @@
         # the __parent__ stack:
         parliament.__parent__ = application
         roles = get_roles(parliament)
+        print "------!!!!--ASHOK--!!!---", roles
         # "bungeni.Clerk", "bungeni.Speaker", "bungeni.MP"
         for role_id in roles:
-            if role_id in ("bungeni.Clerk", "bungeni.Speaker", "bungeni.MP"):
+            if role_id in ("bungeni.Clerk", "bungeni.Speaker", "bungeni.MP", "bungeni.QuestionClerk"):
                 log.debug("adding parliament workspace %s (for role %s)" % (
                                                         parliament, role_id))
                 LD.workspaces.append(parliament)
@@ -402,7 +403,8 @@
         u'bungeni.Minister': interfaces.IMinisterWorkspace,
         u'bungeni.MP': interfaces.IMPWorkspace,
         u'bungeni.Speaker': interfaces.ISpeakerWorkspace,
-        u'bungeni.Clerk': interfaces.IClerkWorkspace
+        u'bungeni.Clerk': interfaces.IClerkWorkspace,
+        u'bungeni.QuestionClerk': interfaces.IQuestionClerkWorkspace
     }
 
     def __init__(self, context, request):

```


## 10) Now we need to define the viewlets for the QuestionClerk workspace in bungeni.ui/workspace.zcml

(Note: below i have simply copied & renamed the viewlets for the Clerk as the QuestionClerk viewlets -- in reality some viewlets
may need to be rewritten to filter out specific items ... ) :

```
Index: viewlets/workspace.zcml
===================================================================
--- viewlets/workspace.zcml	(revision 7561)
+++ viewlets/workspace.zcml	(working copy)
@@ -228,6 +228,66 @@
         weight="40"
     />
     
+
+
+    <!-- question clerk workspace -->
+   <browser:viewlet name="bungeni.workspace.action-required.questionclerk"
+        for="*"
+        manager=".interfaces.IWorkspaceManager"
+        class=".workspace.ClerkItemActionRequiredViewlet"
+        view="bungeni.ui.interfaces.IQuestionClerkWorkspace"
+        permission="zope.View"
+        weight="10"
+    />
+    <browser:viewlet name="bungeni.workspace.working-draft.questionclerk"
+        for="*"
+        manager=".interfaces.IWorkspaceManager"
+        class=".workspace.ClerkItemsWorkingDraftViewlet"
+        view="bungeni.ui.interfaces.IQuestionClerkWorkspace"
+        permission="zope.View"
+        weight="15"
+    />
+    <browser:viewlet name="bungeni.workspace.reviewed.questionclerk"
+        for="*"
+        manager=".interfaces.IWorkspaceManager"
+        class=".workspace.ClerkReviewedItemViewlet"
+        view="bungeni.ui.interfaces.IQuestionClerkWorkspace"
+        permission="zope.View"
+        weight="20"
+    />
+    <browser:viewlet name="bungeni.workspace.items-approved.questionclerk"
+        for="*"
+        manager=".interfaces.IWorkspaceManager"
+        class=".workspace.ItemsApprovedViewlet"
+        view="bungeni.ui.interfaces.IQuestionClerkWorkspace"
+        permission="zope.View"
+        weight="25"
+    />
+    <browser:viewlet name="bungeni.workspace.items-pending-schedule.questionclerk"
+        for="*"
+        manager=".interfaces.IWorkspaceManager"
+        class=".workspace.ItemsPendingScheduleViewlet"
+        view="bungeni.ui.interfaces.IQuestionClerkWorkspace"
+        permission="zope.View"
+        weight="30"
+    />
+    <browser:viewlet name="bungeni.workspace.items-scheduled.questionclerk"
+        for="*"
+        manager=".interfaces.IWorkspaceManager"
+        class=".workspace.ItemsScheduledViewlet"
+        view="bungeni.ui.interfaces.IQuestionClerkWorkspace"
+        permission="zope.View"
+        weight="35"
+    />
+    <browser:viewlet name="bungeni.workspace.sittings.questionclerk"
+        for="*"
+        manager=".interfaces.IWorkspaceManager"
+        class=".workspace.DraftSittingsViewlet"
+        view="bungeni.ui.interfaces.IQuestionClerkWorkspace"
+        permission="zope.View"
+        weight="40"
+    />
+
     <!-- Speaker Workspace -->
 
 <!-- XXX-INFO-FOR-PLONE - MR - 2010-05-03
@@ -335,6 +395,14 @@
         permission="zope.View"
         weight="50"
     />
+    <browser:viewlet name="bungeni.workspace.questionclerk-item-archive"
+        for="*"
+        manager=".interfaces.IWorkspaceArchiveManager"
+        class=".workspace.AllItemArchiveViewlet"
+        view="bungeni.ui.interfaces.IQuestionClerkWorkspace"
+        permission="zope.View"
+        weight="50"
+    />
     <browser:viewlet name="bungeni.workspace.speaker-item-archive"
         for="*"
         manager=".interfaces.IWorkspaceArchiveManager"

```


## 11) Finally create the office in the system

login as an admin in the current parliament add a new office of the type "Questions Office". Create a user in the parliament add the user to the office.
Now login as the user -- and the workspace should be that of the Questions Office.