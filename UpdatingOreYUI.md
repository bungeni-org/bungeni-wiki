# Introduction

ore.yui is a Zope 3 packaging of YUI and is extensively used in Bungeni. The instructions below detail how to update the package with newer versions of YUI.


# Details
  * Download ore.yui source.
```
svn checkout https://svn.objectrealms.net/svn/public/ore.yui/trunk .
```
  * Edit rname and version variables at the top of setup.py to the version of YUI and the version number of the egg that you are creating respectively.
  * A MANIFEST.in file must be created within the src folder with the following lines
```
include *.py *.zcml *.txt
recursive-include src/ore/yui/resources *.*
```
  * Create the egg. setup.py downloads the version of YUI specified above and unpacks it
```
python ./setup.py bdist_egg
```