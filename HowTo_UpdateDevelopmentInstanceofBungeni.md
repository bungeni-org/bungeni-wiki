# Introduction

A development instance requires a slightly different setup to that of a deployment instance.
This page aims to document these differences


# Managing Version Pegs

The default buildout employs version pegs to fix versions of packages used by bungeni.
This is done primarily in 2 places :
  1. in the versions.cfg file in the buildout root
  1. in the externals.txt in the buildout src folder

The case of (2) is where we have packages directly updated by bungeni developers typically the projects named bungeni.**i.e. bungeni.models, bungeni.ui, bungeni.core etc.**

To turn your instance into a development instance you will need to update these packages to HEAD. to do that do the following :
```
## from the buildout folder cd to src
cd src
## update the specific package to head
svn up bungeni.portal/ -rHEAD
```

From then onward be careful not to do an svn update inside src, as this will revert all packages updated to HEAD back to the pegged revision :
```
cd src
## DO NOT DO THIS !!!
svn up
```

Instead update only the specific package that you have updated  to HEAD :
```
cd src
## THIS IS THE CORRECT WAY
svn up bungeni.portal/
```

There is also a helper script in src to make this easier :
```
cd src
## 
./svnup_head.sh bungeni.portal
```

# Managing deploy.ini

deploy.ini is the file used by paster for startup configurations, entry points etc.

If you change deploy.ini and you want the changes reflected your running instance - restarting the service using the updated deploy.ini in supervisor will not suffice.

The recommended way is to shutdown supervisor completely and restart it.

For capistrano instances you can restart supervisor using :
```
cap bungeni_tasks:stop_supervisor bungeni_services:start_bungeni
```

For regular instances, you can simply kill the supervisor process after shutting down the services within supervisor :
```
ps ax | grep supervisor
kill -9 <the process id of the supervisor service>
```