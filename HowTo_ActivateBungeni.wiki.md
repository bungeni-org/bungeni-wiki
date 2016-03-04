

## Introduction

After Installing Bungeni to be able to use the system, certain activation configurations need to be done within the system.

## Manual Activation

Activation can be done manually by browsing to the respective parts of the system.

|Step no	|Step desc	|Expected Outcome|
|:-------|:---------|:---------------|
|1	      |Access bungeni login screen, http://bungeni/login	|Login screen visible|
|2	      |Login to bungeni as admin	|Successfully logged in – default admin workspace is displayed with 'State:Draft – Group not yet active' displayed on the right hand side|
|3	      |Browse to the archive, by clicking on the 'archive' tab (http://bungeni/archive/browse/parliaments). | You are taken to a page listing the registerd parliaments in the system (VI, VII, VIII, IX).|
|4	      |Click on the last parliament IX	|You are taken to a page with sub tabs providing details about the 9th parliament|
|5	      |Activate the IX parliament. Select the drop down 'State; Draft group not yet active' and click activate. You will be taken to a screen to activate the current parliament. Enter a comment and click the activate button.|The parliament should be activated (It should say 'Active Group' on the top right hand side)|


Subsequently -- Offices need to be added and activated for the Clerks office and for the Speaker. The manual steps are listed below.

|Step no	|Step desc	|Expected Outcome|
|:-------|:---------|:---------------|
|1       |	Assuming test 1 was Successful   browse to the activated Parliament IX and expand the the 'Bungeni Portal' menu under : Browse->Parliaments ->IX	|Able to browse to active parliament ix|
|2       |	Click Offices on the left hand menu Browse->Parliaments ->IX->Offices |Listing of offices (presently none)|
|3       |	Click 'Add Office' in the top right hand part of the screen	|A form to Add a new Office is displayed|
|4       |	Fill up the form – call it 'ClerkOffice' and click submit (Remember to select 'ClerkOffice' in the combo-box drop-down above the submit button	|You should be able to see the Clerk's Office in the Offices listing|
|5       |	Activate the Clerk's Office by browsing to it and  changing the group state to 'Active' in the top right hand corner	|Able to activate the 'ClerkOffice' (Should show the state as 'Active Group' in the top right hand corner)|
|6       |	Browse to  'Office Members' of the 'Clerk Office' -- Click on 'ClerkOffice' in the Offices listing and    on the left hand menu below the highlighted 'Clerk Office' click on 'Office member'|	You will see a listing of Office members in the clerk's office (an empty listing at present)|
|7       |	Add an Office member -- On the top right hand corner click 'Add Office Member'|You will see a Form to add an Office member to the Clerk Office|
|8       |	Select a name – and save the 'Office member'	|Office member should be successfully added.|
|9       |	The iteration for the Speakers Office is identical – instead of selecting 'ClerkOffice' select 'Speaker office'	 |                |

After this -- Government, Ministries and Committees can be activated as required.

## Automated Activation

Automated activation can be done via  selenium script. Note: this will only work on a freshly installed system where no parliament has been activated. Usually that is the state right after a reset-db and load-demo-data has been run on the Bungeni installation.

  1. Download and install Selenium IDE into your firefox installation from [Selenium](http://www.seleniumhq.org)
  1. Checkout the latest version of the Activate Bungeni test case from subversion :
```
svn export http://bungeni-portal.googlecode.com/svn/SeleniumTestSuites/
```
  1. The script we are interested in is called ActivateBungeni
  1. Now launch firefox - and make sure Bungeni has been started up. Make sure you are logged out of Bungeni. Go to the Bungeni login page. (directly on port 8081)
  1. Now go to tools -> Selenium IDE in firefox, and in the Selenium screen open the 'ActivateBungeni' test case.
  1. make sure that the base url for the selenium test case is set to the current bungeni installation home url.
  1. Set the speed of the test to "Slow", and click run test case.
  1. The test case will take about 5 - 8 minutes to run - at the end you will have an activated Bungeni installation
  1. The test case also activates all the ministries and committees for the parliament.