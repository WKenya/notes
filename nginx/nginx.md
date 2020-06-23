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

## Configuration

Two important ideas:

### Terms

#### Directive

- Specific configuration options set in configuration. ex: `server_name mydomain.com`

#### Context

- Sections within the configuration, denoted by curly braces. Directives are located inside these. Can be nested and inherit from their parent scope.

The top most level context is the __Main Context__, where we set the global directives for the main nginx process.

Other important contexts are _http_, which is for the protocol, and _server_, which is the virtual host, and _location_ which matches uri location requests to the parent server context.

### Creating a Virtual Host

Open `/etc/nginx/nginx.conf` in a text editor. Make a backup before you perform any modifications if needed.

Remove everything in the file and have the following left:

```nginx
events { }

http { }
```

_Note:_ events is needed to be considered a valid nginx file.

#### Listen

A virtual host is a `server { }` context in the file. A virtual host is responsible for listening on a port for a given ip address or domain. For `http`, this is usually port 80, or port 443 for `https`.

nginx will assume port 80 under the `http` protocol, but it is good practice to have it.

```nginx
listen 80;
```

#### Server Name

We can also set the server name, which is the domain, subdomain, or ip address:

```nginx
server_name 192.168.242.92;
```

You can also wildcard the server domain with a `*`, so you can wildcard a subdomain if you desire.

#### Root Path

The root path from where nginx will serve files from for any requests.

So if there is a request for `/images/cats.png`, nginx will append the root path of `/sites/demo` to `images/cats.png` to find the file on the filesystem `/sites/demo/images/cats/png`.

```nginx
root /sites/demo
```
