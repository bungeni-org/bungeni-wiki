

# Introduction

The different components of Bungeni run over different HTTP ports.
The defaults for these ports are listed below, and also the methodology for changing the deployment ports is provided.
There can be various reasons to want to change default ports, the most common reason is usually security considerations.
Note that all the services listed below, are by default mapped to the localhost. For security reasons we recommend not changing the default host configuration (e.g. changing the Bungeni service to listen on a public host name). This page discusses only the aspect of changing the default ports.

# Default Ports

|Component | Port | Host |
|:---------|:-----|:-----|
|Bungeni   | 8081 | localhost |
|Plone     | 8082 | localhost |
|Deliverance Proxy | 8080 |localhost |
|Deliverance Static | 8083 |localhost |
|eXist XML DB | 8088 | localhost |
|Supervisor Process monitor | 8888 | localhost |
|Varnish Cache | 8000 | localhost |

# Changing Default Ports

Default ports can be changed via the fabric [setup.ini file](http://code.google.com/p/bungeniHowTo_SetupFabricScripts)

## Changing Port for Bungeni and Plone

Ports for Bungeni and Plone can be changed via the `http_port` parameter in setup.ini,

for bungeni --

```
[bungeni]
....
http_port = 8081
```

for plone --
```
[plone]
....
http_port = 8082
```

After changing the port you will need to regenerate the appropriate application configurations.

If you change the bungeni port, run :

```
./fl config_ini:bungeni
```

If you change the plone port, run :

```
./fl config_ini:plone
```

You will need to restart the bungeni / plone service via supervisor after running `config_ini`.


## Changing Port for Deliverance

The Deliverance component is composed of 2 different services, one to serve static files and the other that serves as an application proxy.

To change the port of the _Deliverance Application Proxy_ , change the `http_port` parameter in the `portal` section :
for plone --
```
[portal]
....
http_port = 8080
```

To change the port of the _Deliverance Static_, change the `static_port` parameter in the `portal` section :
```
[portal]
....
static_port = 8083
```

After changing the ports you will need to regenerate the configuration :

```
./fl config_ini:portal
```

and then restart both the `static` and `portal` services in supervisor.

## Changing Port for Exist

To change the `exist` service port, change the `http_port` parameter in the `exist` section.

```
[exist]
...
http_port=8088
...
```

Unlike the other services, changing the eXist port requires regenerating the `supervisor` service configuration file, this implies restarting supervisor which means all the services will also need to be restarted afterwards.

First stop supervisor :
```
./fl stop_monitor
```

Regenerate the supervisor configuration using the following command :
```
./fl config_supervisord
```

Start supervisor :
```
./fl start_monitor
```

Then start all the other services via supervisor

## Changing Port for Supervisor Monitor

The port for the supervisor can also be changed via `setup.ini`, change the `port` parameter in the `supervisord` section :
```
[supervisor]
...
port=8888
```

You will need to regenerate the supervisor configuration and restart supervisor as described in the previous section.




