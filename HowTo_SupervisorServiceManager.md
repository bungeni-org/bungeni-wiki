#summary One-sentence summary of this page.
#labels bungeni-wiki

# Introduction

The supervisor service manager is used to start / stop the bungeni services.

Supervisor can be accessed via a browser -- on the host, port specified [in the fabric setup parameters](http://code.google.com/p/bungeni-portal/wiki/HowTo_ConfigureFabricIni#supervisor_host_,_supervisor_port)

![http://bungeni-portal.googlecode.com/files/supervisor-exist.png](http://bungeni-portal.googlecode.com/files/supervisor-exist.png)

This page documents controlling the supervisor monitor using fabric commands run on the command line.

# Start, Stop the service monitor

Starting :

```
fab start_monitor
```

Stopping :

```
fab stop_monitor
```

# Service Startup Order

Some of the services are dependent on other services and need to be started up in a particular order. The recommended order of startup is shown below :

  1. postgres
  1. rabbitmq
  1. serializer

Once the above have been started up the other services can be started up in any order.

# Service Dependencies

Some services are dependent upon others for specific functionality to work correctly , these dependencies are listed below :

|Service | Depends On|
|:-------|:----------|
|bungeni | postgres, rabbitmq, openoffice, serializer |
|plone   | bungeni   |
|portal  | static    |
|exist-sync | exist, rabbitmq|
|serializer | postgres, rabbitmq |


# Start, Stop specific services

## bungeni

Starting :

```
fab start_service:bungeni
```

Stopping :

```
fab stop_service:bungeni
```

## postgres

Starting :

```
fab start_service:postgres
```

Stopping :

```
fab stop_service:postgres
```

## plone

Starting :

```
fab start_service:plone
```

Stopping :

```
fab stop_service:plone
```

## exist

Starting :

```
fab start_service:exist
```

Stopping :

```
fab stop_service:exist
```

## exist sync

Starting :

```
fab start_service:exist-sync
```

Stopping :

```
fab stop_service:exist-sync
```

## rabbitmq

Starting :

```
fab start_service:rabbitmq
```

Stopping :

```
fab stop_service:rabbitmq
```

## deliverance portal

Starting :

```
fab start_service:portal
```

Stopping :

```
fab stop_service:portal
```

## varnish

Starting :

```
fab start_service:varnish
```

Stopping :

```
fab stop_service:varnish
```