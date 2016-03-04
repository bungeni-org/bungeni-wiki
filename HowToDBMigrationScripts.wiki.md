# Introduction

Bungeni uses alembic to handle data migration for existing databases every time the schema changes.


# Create migration script

From the bungeni root directory run the following command. Please use a descriptive message
```
/bin/alembic revision -m "create example table"
```

# Add upgrade and downgrade functions

A file with a name close to the message input above will be created in data/alembic/versions folder in the bungeni root folder. Modify this file and add the specific db migration steps.

You MUST specify both upgrade and downgrade functions. For more information have a look at the [alembic docs](https://alembic.readthedocs.org/en/latest/tutorial.html#create-a-migration-script)