

# Introduction

A live system is one which will be used by end users on an ongoing basis. There are many things to be aware of when setting up such a system. This page aims to document some of these steps

# Version Control

Bungeni is setup by checking out code from the trunk branch of the bungeni svn repository.
For live installations it is desirable to not to run code from trunk - but to run the system on specific tagged versions of the source code. This ensures stability and predictability - and also allows rolling back or forward to downgrade or upgrade a change.

Tagged release of bungeni are listed in [this RELEASES document](https://spreadsheets.google.com/pub?key=0ApKt_FmivEH8dF9ITXRqMFNKRDNQZlJBbGtZcnVwRUE&hl=en&gid=0).

Running a svn up to the tagged revision and running a buildout effectively reverts the source code to the specific revision. If there was a schema change between revisions - please see the issue below.


# Linux configurations

## Shared memory
Increasing the kernel.shmmax variable makes available more shared memory for postgresql.

This will change the maximum shared memory at runtime to 64MB
```
sudo /sbin/sysctl -w kernel.shmmax=67108864
```

To make the change permanent add an entry into /etc/sysctl.conf :
```
kernel.shmmax = 67108864
```

As a rough measure 15% of system memory can be set to the shared memory max variable.

Set shared buffers for postgres (in postgresql.conf) to 96 mb :
```
shared_buffers=96mb
```

## Maximum file-descriptors

The default setting on the maximum number of files opened by a process is set at 1024, this could be low on a very high-volume Bungeni installation. As a precaution, you can increase the limit by adding:

```

fs.file-max=100000

```

at the bottom of **/etc/sysctl.conf** and reboot.

# Schema Changes

Managing schema changes has been documented here : Managing\_Bungeni\_Schema\_Upgrades

# Backups

  * Any local patches to source code need to be backed up and documented, preferably via the issue tracker.
  * The live database needs to be incrementally backed up at regular intervals (e.g. half hourly)
### backup.sh

Backs up the db and ftps the backup to a different server.
```
#!/bin/bash
file_name=`date +%Y-%m-%d-%H%M%S`.txt
pg_dump bungeni -U undesa  > $file_name
./ftpfile.sh $file_name
```

### ftpfile.sh

Ftp script to upload a file to a server

```
#!/bin/bash
echo on
HOST='192.168.0.48'
USER='undesa'
PASSWD='undesa'
FILE=$1
ftp -n 192.168.0.48 <<END_SCRIPT
user $USER $PASSWD
binary
cd live_backup
put $FILE
quit
END_SCRIPT
exit 0
```

### crontab entry

Run backup every 20 minutes

```
*/20 * * * * /home/undesa/bin/backup.sh
```


# Installation Administration

## Minimal metadata

To commence data-entry - you need to start with a clean system without any parliaments or users. However, the system requires some minimal metadata to get started with -- these include :
  * country codes
  * language codes
  * committee types
  * parliamentary office types
  * sitting types
  * address types
  * user role types
  * venues / meeting rooms within parliament

The minimal metadata does not have a user interface for adding it to the system - this is loaded via a sql script :
```
./bin/reset-db
./bin/psql bungeni < ./testdatadmp/dumpmin-undesa.txt
```
The script is called dumpmin-undesa.txt and can be found in the `testdatadmp` folder of the bungeni buildout installation.


## Users


## Reversing incorrect data entry

TODO