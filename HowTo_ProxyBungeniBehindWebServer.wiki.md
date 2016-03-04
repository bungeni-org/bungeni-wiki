

# Introduction

Bungeni when deployed with deliverance portal can be proxied behind a webserver. This page provides an example of how to do this.


# Setting Deployment Configurations

Deployment configurations can be done via fabric actions. Assuming you want to deploy bungeni on the domain `test.bungeni.org` ; where deliverance is running on port 8080 and listening on `localhost` you will need to do the following :

## Set configuration parameters

For a detailed explanation see [Portal Deployment Configuration](http://code.google.com/p/bungeni-portal/wiki/HowTo_ConfigureFabricIni#portal_deployment_configuration).

In a nutshell you would set :
```
[global]
....
app_host=localhost

[portal]
....
http_port=8080
....
web_server_host=test.bungeni.org
web_server_port=80
```

And then run `fab -H localhost config_ini:portal config_supervisord` which will generate the appropriate proxying configuraitons for deliverance.

## Virtual Hosting

Finally you need to proxy bungeni behind a virtual host.

### nginx configuration

The following is [nginx](http://www.nginx.org) configuration for deploying bungeni :

```

upstream app_server {
	server 127.0.01:8080 weight=1;
}

server {
	listen 80;
	server_name test.bungeni.org;
	access_log  /var/log/nginx/bungeni-access.log;
	location / {
		include /etc/nginx/proxy.conf;
		proxy_pass http://app_server;
	}
}

```

Add the above configuration in a file into the `/etc/nginx/sites-enabled` folder.

The proxy configuration for nginx is the default one and looks like this :

```
proxy_redirect          off;
proxy_set_header        Host            $host;
proxy_set_header        X-Real-IP       $remote_addr;
proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
client_max_body_size    10m;
client_body_buffer_size 128k;
proxy_connect_timeout   90;
proxy_send_timeout      90;
proxy_read_timeout      90;
proxy_buffers           32 4k;
sendfile on;
```
**Check that the above configuration exists in a file at this location /etc/nginx/proxy.conf
If the file does not exist then create it and add the specified configurations.**


### Apache configuration

For apache you need to enable mod\_proxy , mod\_proxy\_http and mod\_rewrite.
The virtualhost configuration looks like this :

```
 
<VirtualHost *:80>
    ServerName bungeni.org
    ServerAlias test.bungeni.org
    ErrorLog "/var/log/www/error.bungeni.org.log"
    CustomLog "/var/log/www/access.bungeni.org.log" combined
    ProxyPreserveHost On
    RewriteEngine On
    RewriteLog "/var/log/rewrite.bungeni.org.log"
    RewriteRule ^/(.*) http://localhost:8080/$1 [P]
</VirtualHost>

```

