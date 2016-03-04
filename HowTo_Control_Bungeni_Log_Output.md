# Introduction

The logging configuration of bungeni is specified in debug.ini and controls what level of log messages are outputted by the system.


# Logging System

Python logging is roughly based on JSR-47 and apache log4j. A detailed explanation about the logging system used in python can be found here [python logging](http://www.red-dove.com/python_logging.html).

# Usage

The following is an extract from debug.ini for bungeni, the individual sections have been described below  :
```
[loggers]
keys = root, zope, sqlalchemy, bungeni, bungeniportal, bungenicore
#, bungeni.portal

[handlers]
keys = console, file

[formatters]
keys =
#generic

[logger_root]
level = WARN
handlers =

[logger_zope]
level = DEBUG
propagate = 1
qualname = zope
handlers =
channel = zope
parent = (root)

[logger_bungeni]
level = ERROR
propagate = 1
qualname = bungeni
handlers =
channel = bungeni
parent = (root)

[logger_bungenicore]
level = WARN
propagate = 1
qualname = bungeni.core
handlers =
channel = bungeni
parent = (root)
```


The 'loggers' section specifies which area of the section we desire to log
```
[loggers]
keys = root, zope, sqlalchemy, bungeni, bungeniportal, bungenicore
```

Each key in loggers corresponds to a logger section in the ini file , for e.g. the following is the section for bungeni.core. The section follows the naming convention :  [logger

&lt;keyname&gt;

] . The 'qualname' name parameter specifies the package we want this logger to filter on. In the below example it filters messages of the level 'WARN' in 'bungeni.core'.

```
[logger_bungenicore]
level = WARN
propagate = 1
qualname = bungeni.core
handlers =
channel = bungeni
parent = (root)
```

Since packages are hierarchical, it is possible to hierarchically stack loggers. For e.g. if we want to globally log only level 'ERROR' for  packages under 'bungeni' but we want to log 'bungeni.core' (within 'bungeni') with a 'DEBUG' level - we can stack loggers with the following configuration  :

```
[logger_bungeni]
level = ERROR
propagate = 1
qualname = bungeni
handlers =
channel = bungeni
parent = (root)

[logger_bungenicore]
level = DEBUG
propagate = 1
qualname = bungeni.core
handlers =
channel = bungeni
parent = (root)
```

the 'propagate' parameter tells the logger to consult the parent for its logging level if a level isnt specified in the configuration for the logger.