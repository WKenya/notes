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

Run `systemctl reload nginx` to prevent any downtime. If the configuration is wrong, the service will stay up with the old config.

Run `nginx -t` to verify that the file is configured properly.

#### Types Context

This context allows you to configure the mime type for files being requested.

```nginx
types {
    text/html html;
    text/css css;
}
```

But there is an easier way. In `/etc/nginx/mime.types` there is a configuration file that already contains this info. We can import that into our current configuration. We can do this by using the `include` directive and point towards that file.

```nginx
include mime.types;
```

### Location Blocks

Location blocks use URIs to configure behavior for the given values. This is useful if you don't just want to serve a static file when a resource is requested.

#### Prefix Match

```nginx
location /greet {
    return 200 'Hello from NGINX /greet location';
}
```

_Note_: This is a prefix match, meaning anything starting with /greet will be matched, including /greeting

#### Exact Match

To do an exact match, add an `=` between the location and URI.

```nginx
location = /greet {
    return 200 'Hello from NGINX /greet location';
}
```

#### Regex Match Case Sensative

To do a regex match, add an `~` between the location and the regex field.

_Note:_ This is case sensitive by default.

```nginx
location ~ /greet[0-9] {
    return 200 'Hello from NGINX /greet location';
}
```

#### Regex Match Case Insensitive

To do a case insensitive match, add an asterisk next to the tilde.

```nginx
location ~* /greet[0-9] {
    return 200 'Hello from NGINX /greet location';
}
```

_Note_: nginx assigns priority to locations, with regex's taking higher priority over prefix matchers.

#### Preferential Prefix

To assign a higher precedence for a Prefix Match, add a `~^` to the location.

```nginx
location ^~ /Greet2 {
    return 200 'Hello from NGINX /greet location';
}
```

#### Priority of Matches

1. Exact
2. Preferential Prefix
3. Regexes of both types, though the first block in order is prioritized if there are multiple
4. Prefix match

### Variables

There are two types of variables:

#### Configuration Variables

These are ones we set ourselves.

```nginx
set $var 'something';
```

#### Nginx Module variables

Provided through Nginx or other modules.

Most are listed here: [https://nginx.org/en/docs/varindex.html]([https://nginx.org/en/docs/varindex.html)

```nginx
$http, $uri, $args
```

`$host` is the request IP

`$uri` is the resource path

`$args` are the query string params

- You can use `_` after the `$args` variable to specify which query parameter needed.
- Ex: `$args_name` returns the value of the name param

##### Setting Variables

Use `set` syntax to set a value of a variable. Variables can be set to integers, strings, or booleans.

- EX: `set $weekend 'No'`;

#### Conditionals

Useful for checking variables before proceeding through different sections of the nginx config.

```nginx
if ( $arg_apikey != 1234 ) {
    return 401 "Incorrect API Key";
}
```

_Note_: The use of conditionals inside locations blocks is [highly discouraged](https://www.nginx.com/resources/wiki/start/topics/depth/ifisevil/). This can lead to unexpected behavior.

Use `~` to perform a regex search on a variable.

```nginx
if ( $date_local ~ 'Saturday|Sunday') {
    set $weekend 'Yes';
}
```

### Rewrites and Redirects

Both are methods for returning different content than originally specified from the URI.

#### Rewrite

Allow us to take a URI request and map it to a resource at a different URI internally.

```nginx
rewrite pattern URI;
```

```nginx
rewrite ^/user/\w+ /greet;

location /greet {
    return 200 "Hello User";
}
```

__Note__: When a URI is rewritten, it is completely reevaluated from the start as a new request. This means that rewrites require more resources server side when evaluating requests over redirects.

##### Capture Groups

You can use RegEx capture groups to capture parts of the original request URI.

```nginx
rewrite ^/user/(\w+) /greet/$1;

location = /greet/john {
    return 200 "Hello John";
}
```

##### Last Flag

This flag allows us to tell nginx to not rewrite a URI anymore. Useful if you have multiple rewrites that might clash.

```nginx
 rewrite ^/user/(\w+) /greet/$1 last;
 ```

#### Redirect

Allows us to send a 300 style code back to indicate the resource is now somewhere else. Most Clients when then use the new URI to get the resource automatically.

```nginx
return 3** URI;
```

Ex:

```nginx
location /logo {
    return 307 /thumb.png;
}
```

_Note_: This URI can be relative or absolute. For `300s` we can provide a URI to redirect instead of a string like a `200`.

### Try-Files and Named Locations

Can be used in the server context or location context.

Allows nginx to look for resources in multiple locations __relative to the root__ directory, with the first one to exist being served back. The final argument rewrites (and reevaluation of) the request if the previous URIs fail to work.

```nginx
try_files path1 path2 finals;
```

Ex:

```nginx
server {
    # etc...
    root /sites/demo;

    try_files /thumb.png /greet;
}
```

This is typically used with variables to make it useful. The `$uri` variables allows the initial URI requested to be check first.

```nginx
try_files $uri /cat.png /greet /friendly_404;

location /friendly_404 {
    return 404 "Sorry, that file could not be found.";
}
```

#### Named Locations

Allows us to assign names to a location context. Apply an `@` on a location context, and then use that to reference it.

```nginx
try_files $uri /cat.png /greet @friendly_404;

location @friendly_404 {
    return 404 "Sorry, that file could not be found.";
}
```

### Logging

Inform us of what is happening to our server. There are two types of logs:

#### Error Logs

Anything that fails or didn't happen as expected.

_Note:_ It is a common misconception that 404's log here. They typically do not, unless a resource is actually missing on the server. These should be properly handled through the nginx conf, rather than letting nginx search for the resource and failing.

Any issues with the configuration file will also log out to this file.

#### Access Logs

Logs every request that happens on the server.

We can create our own logs for special purposes.

```nginx
location /secure {
    access_log /var/log/nginx/secure.access.log;
    return 200 "Welcome to secure area.";
}
```

_Note:_ Access to this location will now _not_ log to the regular access log. This can be enabled again by adding a specific access log directive to the normal directory.

```nginx
location /secure {
    access_log /var/log/nginx/secure.access.log;
    access_log /var/log/nginx/access.log;
    return 200 "Welcome to secure area.";
}
```

You can also disable logging for requests to reduce server load and processing.

```nginx
location /secure {
    access_log off;
    return 200 "Welcome to secure area.";
}
```

Generally speaking, turn the access log off unless you know you need it as it is written to frequently. For more advanced configuration, see [here](https://docs.nginx.com/nginx/admin-guide/monitoring/logging/).

### Inheritance and Directive Types

An nginx context inherits configurations from it's parents context. This means that the root will be inherited throughout cascading inheritances.

```nginx

server {
    root /site/demo;

    location foo {

        # inherited root
        # root /sites/demo
    }
}
```

There are 3 types of Directives:

#### Standard Directive

- Can only be declared once. A second declaration overrides the first.
- Gets inherited by all child contexts
- Child context can override inheritance be re-declaring directive
- Ex: `root`

##### Index Directive

When nginx receives a request to a directive, it will utilize the `index` directive to know what resource to serve by default. The directive takes multiple URIs in case one does not exist.

```nginx
index index.php index.html;
```

#### Array Directive

- Can be declared multiple times without overriding the previous settings
- Gets inherited by all child contexts
- Child context can override inheritance by re-declaring the directive.
- Ex: `access_log`

#### Action Directive

- Invokes an action such as a rewrite or a redirect
- Inheritance does not apply as the request is either stopped (redirect/response) or re-evaluated (rewrite).
- Ex: `return`, `rewrite`, `try_files`

### PHP (Dynamic Services)

We can utilize nginx as a reverse proxy to serve dynamic content via a PHP service. This will use the FastCGI binary protocol to communicate between nginx and php-fpm.

To do this, we need to find the php-fpm socket the nginx will use to communicate to the service. We can find this by performing a search of the sockets on the system: `find / -name *php.sock`

Using the name of the socket, we can now configure nginx to use this socket:

```nginx
location ~\.php$ {
      # Pass php requests to the php-fpm service (fastcgi)
      include fastcgi.conf;
      fastcgi_pass unix:/run/php/php7.2-fpm.sock;
}
```

_Note:_ nginx provides a `fastcgi.conf` file for us in its default configuration location.

When we try to serve a php file from, we end up getting a 502, and an error in the error logs due to there being a permissions error. This is due to the nginx worker having a user of `nobody`, a default, and php using `www-data`.

We can fix this by adding the user to the configuration file:

```nginx
user www-data;
```

### Worker Processes

The Master process is the actual service that runs nginx. It spawns worker processes that listen and respond to requests. To add more workers we can add a directive to the configuration. This does not necessarily speed up nginx, as the number of processes is only as useful as the amount of core's the CPU has.

```nginx
worker_processes 2;
```

To find out the amount of cores on your CPU, run `nproc`. For more detailed info, run `lscpu`.

However, it is better to set this value to `auto` instead, as nginx will then appropriately make as many workers as there are cores for the CPU, without you needing to specify that.

```nginx
worker_processes auto;
```

#### Worker Connections Directive

This directive tells the worker process the maximum connections they can accept. This can be determined by running `ulimit -n`.

```nginx
events {
    worker_connections 1024;
}
```

Therefore the maximum connections an nginx server can request: `worker processes * worker connections = max connections`.

_Note:_ These are the two most important directives for maximizing the performance of a nginx server.

#### PID Directive

You can also specify what `pid` file you want nginx to use without needing to recompile.

In the global context, write the following:

```nginx
pid /var/run/new_nginx.pid;
```

### Buffers and Timeouts

This is a relatively complex topic, and highly related to the types of requests you will be receiving. Leave these to default values unless you know what you are doing.

Buffering is the process of storing data, in RAM temporarily, between reading and writing.

Timeouts are the maximum time a request send before being stopped.

#### Buffer Directives

Buffer directives support the following notations for data size, case insensitive:

```nginx
buffer_directive 100;  # btyes
buffer_directive 100K; # kilobtyes
buffer_directive 100M; # megabtyes
```

A useful directive for skipping buffering of static files is `sendfile`. This sends static files right from disk to the response.

```nginx
sendfile on;
```

To optimize this, use `tcp_nopush`.

```nginx
tcp_nopush on;
```

These make a big difference on serving static files.

#### Timeout Directives

Timeout directives support the following notations for time, case insensitive:

```nginx
timeout_directive 30;  # milliseconds
timeout_directive 30s; # seconds
timeout_directive 30m; # minutes
timeout_directive 30h; # hours
timeout_directive 30d; # days
```

### Adding Dynamic Modules

We can add more functionality to nginx by rebuilding from source with the modules specified at build time. This allows us to add dynamic modules to nginx.

Dynamic modules are modules that are not always running, and dynamically load as directed by the configuration file, do some work, then stop.

In order to build, rebuild, or upgrade nginx, download the desired source code and build it. The build process will remove any old binaries in the process.

#### Upgrading nginx

In order to upgrade:

1. Don't change the existing configuration of the build. Run `nginx -V` to get the current configuration from the build. Copy the arguments.

2. Determine which modules you would like to add to nginx. Run `./configure -help` to see the entire list.

    - To find dynamic modules filter the list with `./configure --help | grep dynamic`.

3. Find the dynamic module desired and add that to the configuration arguments: `... --with-http-_image_filter_module=dynamic`

    - Notice the dynamic after the equals sign, that tells nginx this is a dynamic module.

4. Add the module path to the configuration arguments so nginx knows where the modules are saved: `... --modules-path=/etc/nginx/modules`

    - This allows us to easily reference the modules in the configuration with the relative path with `load_modules`

5. Run the configuration command.

    - You may get errors if you are missing any dependencies of the modules when building. Install those to continue.

6. Run `make`, then `make install`.

7. Run `nginx -V` to verify the new configuration is correct.

8. Reload nginx and check its status to verify everything is running.

#### Add Dynamic Module to Configuration

Now that nginx has had the plugin built into it, we can now try using the new dynamic modules in configuration.

Look at the documentation of the plugin and add the desired configuration.

```nginx
location = /thumb.png {
    image_filter rotate 180;
}
```

We will also need to configure nginx to load the module in the configuration before this works.

In the global context, add the following:

```nginx
load_module modules/ngx_http_image_filter_module.so
```

_Note:_ Modules will end in the .so file extention.

