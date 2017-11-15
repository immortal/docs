+++
description = "Nginx example"
title = "Using Nginx"
date = "2010-03-26T23:14:08+02:00"

+++

To expose the services via web [Nginx](https://nginx.com) can be used.

Configuration example:

```nginx
upstream immortal {
    server unix:/var/run/immortal/sleep/immortal.sock;
}

server {
listen 80 default_server;
server_name _;
location / {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header X-NginX-Proxy true;
    proxy_http_version 1.1; # for keep-alive
    proxy_pass http://immortal/;
    proxy_redirect off;
    }
}
```

Based on the setup, permissions for the socket may need to be changed:

```sh
$ chmod 766 /var/run/immortal/<service name>/immortal.sock
```

To check the service status:

```sh
http://<domain>
```

To send signals:

```sh
http://<domain>/signal/<signal>
```

For example to stop the service:

```sh
http://<domain>/signal/stop
```

To start the service:

```sh
http://<domain>/signal/start
```

The responses are in JSON format.
