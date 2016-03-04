#summary How to install Varnish Cache
#sidebar TableOfContents

# Introduction

Varnish is a web application accelerator that will cache content from any web server such as Apache or Nginx and serve it up much faster. Varnish is able to do this because it caches various contents meaning that less resource intensive strain is placed on the web / database server, providing a more responsive and stable server environment with higher capacity. Put simply, Varnish will speed up a website quite significantly.

With the right configuration, Varnish can continue serving content even when the server is down.

See [Introduction to Varnish](https://www.varnish-software.com/static/book/Introduction.html)

# Pre-Requisites

You should have completed the following steps :

  * **[How to setup the Pre-requisites](Install_PreRequisites_Fabric.md)**
  * **[How to setup Bungeni Parliamentary Information System](Install_Bungeni_Fabric.md)**
  * **[How to setup Plone Content Management System](Install_Plone_Fabric.md)**
  * **[How to setup eXist-db](Install_eXistdb_Fabric.md)**
  * **[How to setup Deliverance Portal](Install_DeliverancePortal_Fabric.md)**

# Download & Setup varnish

## Installing Varnish

The following command will download and install varnish

```
fab -H <host-name-or-ip> varnish_install config_varnish config_supervisord
```

## Configuring Varnish

The Varnish settings can be changed via the fabric `setup.ini` file. The default settings look like this:

```
...
[varnish]
download_url=http://dist.bungeni.org/cache/varnish/varnish-2.1.5.tar.gz
bind_host=127.0.0.1
bind_port=8000
backend_host=127.0.0.1
backend_port=8080
cache_size=512M
```

### Changing the Backend Server

A backend server is the server providing the content Varnish will accelerate. This can be configured using the following parameters in the `setup.ini` file:

```
[varnish]
...
backend_host=127.0.0.1
backend_port=8080
...
```

Now, when Varnish needs to get content from this backend it will connect to port `8080` on `localhost` (127.0.0.1).

Changing the Varnish settings requires regenerating the `supervisor` service configuration file, this implies restarting supervisor which means all the services will also need to be restarted afterwards.

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

### Changing the Bind Host

The `bind_host` and `bind_port` parameters are where you tell Varnish which server and port it should listen for incoming HTTP requests. For a production environment you would probably make Varnish listen on port 80, which is the default.

To change the varnish bind host, change the `bind_host` and `bind_port` parameters in the varnish section.

```
[varnish]
...
bind_host=127.0.0.1
bind_port=8000
...
```

This specifies that we want Varnish to listen on port `8000` on `localhost` (127.0.0.1) for incomming HTTP requests.

Like before, you will need to regenerate the `supervisor` service configuration file. So

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

### Changing the Cache Size

Since Varnish will be caching to memory, we need to tell it how much space it will use. These can be specified by using values like `256M`, `512M` or even `1G`.

```
[varnish]
...
cache_size=512M
```

Again, you will need to regenerate the `supervisor` service configuration file. So

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

## Starting and Stopping varnish

See [using supervisor to start / stop Varnish](http://code.google.com/p/bungeni-portal/wiki/HowTo_SupervisorServiceManager#varnish)