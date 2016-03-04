

# Querying available commands

Running `fab --list` returns a list of available commands.

```
:~/fabfiles$ fab --list
Available commands:

    build_imaging         Build python imaging for Python 2.5 and 2.4
    build_python24        Builds Python 2.4 from source
    build_python25        Builds Python 2.5 from source
    bungeni_build         Runs the bungeni buildout
    bungeni_build_opt     Runs an optimistic bungeni buildout (-N)
    bungeni_check         Check missing packages for bungeni.buildout
    bungeni_install       Checkout,bootstrap and build bungeni
    bungeni_local_config  Generate a local buildout configuration.
    bungeni_setup         Checks out  & bootstrap bungeni source
    bungeni_setupdb       Sets up the postgresql db
    bungeni_update        Update the bungeni source
    config_supervisord    Generates the supervisor configuration
    db_load_demodata      Load demo data from the testdatadmp folder
    db_load_mindata       Load minimal data from the testdatadmp folder
    essentials            Installs reqd OS packages as specified in distro.i...
    portal_build          Build the portal
    portal_check          Check missing packages for portal.buildout
    portal_install        Setup and builds the portal
    portal_local_config   Generate a local buildout configuration.
    portal_setup          Checkout and bootstrap portal source
    setup_pylibs          Install setuptools & supervisor  Pythons(2.5,2.4)
    start_bungeni         Start bungeni
    start_monitor         Start the supervisord service
    start_portal          Start the portal
    start_postgres        Start postgres
    stop_bungeni          Stop bungeni
    stop_monitor          Stop the supervisord service
    stop_portal           Stop the portal

```

# Running Individual Commands

Individual commands can be run with the following syntax :

```
fab -u user -p password -H host-name command-name
```

For e.g. to run a command `start_bungeni` on the localhost with user-name and password admin / admin :

```
fab -u admin -p admin -H localhost start_bungeni
```

Alternatively you can create a .fabricrc file in your user's home folder (e.g. `/home/undesa` for the `undesa` user) folder with the following contents :

```
user = undesa
password = password-for-the-undesa-user
```

and then you can run the `start_bungeni` command like this :

```
fab -H localhost start_bungeni
```