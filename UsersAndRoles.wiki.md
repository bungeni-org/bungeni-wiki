# Introduction

Objects in the system (Question, Bill, other document types) support the following kinds of permissions : create, edit, read, delete

Roles in the system are groups of users, e.g. 'CurrentMPsInParliament' is a role which contains all the user names of the members of parliament.

# Using Permissions

Object permissions can be set to multiple multiple roles or users. e.g. a Question form in the system can have the following set of default permissions :
  * create - CurrentMPsInParliament
  * read - 

&lt;user-id-of-creating-mp&gt;


  * edit - 

&lt;user-id-of-creating-mp&gt;


  * delete - 

&lt;user-id-of-creating-mp&gt;



Once the question has been created and submitted by the MP, the workflow process changes the permissions of the Question object during workflow transitions.

e.g. once the MP submits the Question to the clerk, the state transition changes the state of the submitted question object as follows :
  * read - 

&lt;user-id-of-creating-mp&gt;

, ClerksOffice (role containing users in the clerk's office)
  * edit - ClerksOffice (""  ""  "")



# Users & Groups (Roles)

Users are created in Bungeni portal, initially all users are the same.
Groups are containers for 1 or more users.
Object permissions are assignable to either a user or to a group.

## System

  * Admin

## Members of Parliament

  * Member of Parliament
  * MPs Staff
  * Committee Member
  * Working Group Member

## Political Groups

  * Party Member

## Parliamentary Staff

  * Speakers Office
  * Clerks Office
  * Debate Record Office (Hansard)
  * Committee Clerks
  * Committee Researchers
  * Parliamentary Staff

## Government

  * Minister
  * Ministry staff