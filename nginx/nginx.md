# nginx

## Overview

Nginx is at its core a high performance, low memory, high concurrency web server.

It achieves this speed by actually being a reverse proxy server.

### Apache vs Nginx

Nginx serves files asynchronously, one process can serve multiple files.

This means server side programming languages can't be embedded in its processes, since processes are not spun off per request like Apache.

Any dynamic content must be forked off to a new process, have the work done and reversed proxied back to nginx to serve.

Nginx has faster service of Static Resources due to this design, as it avoids running any dynamic processes.

Nginx is configured by URI locations, rather than apache's file system approach. This makes Nginx very flexible as it can operate as a load balancer, mail server, etc...

## Installation

nginx can either be installed via your package manager, or compiled from source. The benefit of building from source is that you can add optional plugins easily at build time, some of which are only available there. You are also able to configure the build process to select custom locations of configurations files, logs, and other things.

After installing nginx, if desired to run nginx with systemd, create a service file in systemd's `/lib/systemd/system/nginx.service` with the boilerplate configuration. This config file is available on nginx.com.

If you built from source, make sure to modify this config file to match any custom settings you made, such as PIDFile, ExecStart, etc...

To start nginx via systemd, run `systemctl start nginx`. To stop run `systemctl stop nginx`.

systemd can now tell us the status of the service without using `ps`. Run `systemctl status nginx`.

To enable automatic starting of nginx, run `systemctl enable nginx`.

