# Introduction

Bungeni uses [alembic](https://alembic.readthedocs.org/en/latest/tutorial.html) to handle data migrations every time the schema changes. In all cases ensure you have a backup of your data and test the scripts before running them on a production server.


# Upgrade

To upgrade your data to the current DB schema, run

```
./bin/alembic upgrade head
```

from the bungeni root folder.

# Downgrade

To downgrade your data back to a previous version of the schema, first find out the version you would like to go back to

```
./bin/alembic history
```

then, select the revision number from the output of the command above and downgrade to it

```
./bin/alembic downgrade revision-number
```