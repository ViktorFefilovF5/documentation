---
title: "ngx_http_js_module"
id: "/en/docs/http/ngx_http_js_module.html"
toc: true
---

## `js_body_filter`

**Syntax:** *`function`* | *`module.function`* [*`buffer_type`*=*`string`* | *`buffer`*]

**Contexts:** `location, if in location, limit_except`

Sets an njs function as a response body filter.
The filter function is called for each data chunk of a response body
with the following arguments:

- `r`

    the [HTTP request](https://nginx.org/en/docs/njs/reference.html#http) object
- `data`

    the incoming data chunk,
    may be a string or Buffer
    depending on the `buffer_type` value,
    by default is a string.
    Since [0.8.5](https://nginx.org/en/docs/njs/changes.html#njs0.8.5), the
    `data` value is implicitly converted to a valid UTF-8 string
    by default.
    For binary data, the `buffer_type` value
    should be set to `buffer`.
- `flags`

    an object with the following properties:
    - `last`
    
        a boolean value, true if data is a last buffer.

The filter function can pass its own modified version
of the input data chunk to the next body filter by calling
[`r.sendBuffer()`](https://nginx.org/en/docs/njs/reference.html#r_sendbuffer).
For example, to transform all the lowercase letters in the response body:
```
function filter(r, data, flags) {
    r.sendBuffer(data.toLowerCase(), flags);
}
```

If the filter function changes the length of the response body, the
"Content-Length" response header (if present) should be cleared
in [`js_header_filter`](https://nginx.org/en/docs/http/ngx_http_js_module.html#js_header_filter)
to enforce chunked transfer encoding:
```
example.conf:
 location /foo {
     # proxy_pass http://localhost:8080;

    js_header_filter main.clear_content_length;
    js_body_filter   main.filter;
 }

example.js:
 function clear_content_length(r) {
     delete r.headersOut['Content-Length'];
 }
```

To stop filtering and pass the data chunks to the client
without calling `js_body_filter`,
[`r.done()`](https://nginx.org/en/docs/njs/reference.html#r_done)
can be used.
For example, to prepend some data to the response body:
```
function prepend(r, data, flags) {
    r.sendBuffer("XXX");
    r.sendBuffer(data, flags);
    r.done();
}
```

> As the `js_body_filter` handler
> returns its result immediately, it supports
> only synchronous operations.
> Thus, asynchronous operations such as
> [r.subrequest()](https://nginx.org/en/docs/njs/reference.html#r_subrequest)
> or
> [setTimeout()](https://nginx.org/en/docs/njs/reference.html#settimeout)
> are not supported.

> The directive can be specified inside the
> [if](https://nginx.org/en/docs/http/ngx_http_rewrite_module.html#if) block
> since [0.7.7](https://nginx.org/en/docs/njs/changes.html#njs0.7.7).

## `js_content`

**Syntax:** *`function`* | *`module.function`*

**Contexts:** `location, if in location, limit_except`

Sets an njs function as a location content handler.
Since [0.4.0](https://nginx.org/en/docs/njs/changes.html#njs0.4.0),
a module function can be referenced.

> The directive can be specified inside the
> [if](https://nginx.org/en/docs/http/ngx_http_rewrite_module.html#if) block
> since [0.7.7](https://nginx.org/en/docs/njs/changes.html#njs0.7.7).

## `js_context_reuse`

**Syntax:** *`number`*

**Default:** 128

**Contexts:** `http, server, location`

Sets a maximum number of JS context to be reused for
[QuickJS engine](https://nginx.org/en/docs/njs/engine.html).
Each context is used for a single request.
The finished context is put into a pool of reusable contexts.
If the pool is full, the context is destroyed.

## `js_engine`

**Syntax:** `njs` | `qjs`

**Default:** njs

**Contexts:** `http, server, location`

Sets a [JavaScript engine](https://nginx.org/en/docs/njs/engine.html)
to be used for njs scripts.
The `njs` parameter sets the njs engine, also used by default.
The `qjs` parameter sets the QuickJS engine.

## `js_fetch_buffer_size`

**Syntax:** *`size`*

**Default:** 16k

**Contexts:** `http, server, location`

Sets the *`size`* of the buffer used for reading and writing
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).

## `js_fetch_ciphers`

**Syntax:** *`ciphers`*

**Default:** HIGH:!aNULL:!MD5

**Contexts:** `http, server, location`

Specifies the enabled ciphers for HTTPS requests
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).
The ciphers are specified in the format understood by the
OpenSSL library.

The full list can be viewed using the
“`openssl ciphers`” command.

## `js_fetch_max_response_buffer_size`

**Syntax:** *`size`*

**Default:** 1m

**Contexts:** `http, server, location`

Sets the maximum *`size`* of the response received
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).

## `js_fetch_protocols`

**Syntax:** [`TLSv1`] [`TLSv1.1`] [`TLSv1.2`] [`TLSv1.3`]

**Default:** TLSv1 TLSv1.1 TLSv1.2

**Contexts:** `http, server, location`

Enables the specified protocols for HTTPS requests
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).

## `js_fetch_timeout`

**Syntax:** *`time`*

**Default:** 60s

**Contexts:** `http, server, location`

Defines a timeout for reading and writing
for [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).
The timeout is set only between two successive read/write operations,
not for the whole response.
If no data is transmitted within this time, the connection is closed.

## `js_fetch_trusted_certificate`

**Syntax:** *`file`*

**Contexts:** `http, server, location`

Specifies a *`file`* with trusted CA certificates in the PEM format
used to
[verify](https://nginx.org/en/docs/njs/reference.html#fetch_verify)
the HTTPS certificate
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).

## `js_fetch_verify`

**Syntax:** `on` | `off`

**Default:** on

**Contexts:** `http, server, location`

Enables or disables verification of the HTTPS server certificate
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).

## `js_fetch_verify_depth`

**Syntax:** *`number`*

**Default:** 100

**Contexts:** `http, server, location`

Sets the verification depth in the HTTPS server certificates chain
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).

## `js_header_filter`

**Syntax:** *`function`* | *`module.function`*

**Contexts:** `location, if in location, limit_except`

Sets an njs function as a response header filter.
The directive allows changing arbitrary header fields of a response header.

> As the `js_header_filter` handler
> returns its result immediately, it supports
> only synchronous operations.
> Thus, asynchronous operations such as
> [r.subrequest()](https://nginx.org/en/docs/njs/reference.html#r_subrequest)
> or
> [setTimeout()](https://nginx.org/en/docs/njs/reference.html#settimeout)
> are not supported.

> The directive can be specified inside the
> [if](https://nginx.org/en/docs/http/ngx_http_rewrite_module.html#if) block
> since [0.7.7](https://nginx.org/en/docs/njs/changes.html#njs0.7.7).

## `js_import`

**Syntax:** *`module.js`* | *`export_name from module.js`*

**Contexts:** `http, server, location`

Imports a module that implements location and variable handlers in njs.
The `export_name` is used as a namespace
to access module functions.
If the `export_name` is not specified,
the module name will be used as a namespace.
```
js_import http.js;
```
Here, the module name `http` is used as a namespace
while accessing exports.
If the imported module exports `foo()`,
`http.foo` is used to refer to it.

Several `js_import` directives can be specified.

> The directive can be specified on the
> `server` and `location` level
> since [0.7.7](https://nginx.org/en/docs/njs/changes.html#njs0.7.7).

## `js_include`

**Syntax:** *`file`*

**Contexts:** `http`

Specifies a file that implements location and variable handlers in njs:
```
nginx.conf:
js_include http.js;
location   /version {
    js_content version;
}

http.js:
function version(r) {
    r.return(200, njs.version);
}
```

The directive was made obsolete in version
[0.4.0](https://nginx.org/en/docs/njs/changes.html#njs0.4.0)
and was removed in version
[0.7.1](https://nginx.org/en/docs/njs/changes.html#njs0.7.1).
The [`js_import`](https://nginx.org/en/docs/http/ngx_http_js_module.html#js_import) directive should be used instead.

## `js_path`

**Syntax:** *`path`*

**Contexts:** `http, server, location`

Sets an additional path for njs modules.

> The directive can be specified on the
> `server` and `location` level
> since [0.7.7](https://nginx.org/en/docs/njs/changes.html#njs0.7.7).

## `js_periodic`

**Syntax:** *`function`* | *`module.function`* [`interval`=*`time`*] [`jitter`=*`number`*] [`worker_affinity`=*`mask`*]

**Contexts:** `location`

Specifies a content handler to run at regular interval.
The handler receives a
[session object](https://nginx.org/en/docs/njs/reference.html#periodic_session)
as its first argument,
it also has access to global objects such as
[ngx](https://nginx.org/en/docs/njs/reference.html#ngx).

The optional `interval` parameter
sets the interval between two consecutive runs,
by default, 5 seconds.

The optional `jitter` parameter sets the time within which
the location content handler will be randomly delayed,
by default, there is no delay.

By default, the `js_handler` is executed on worker process 0.
The optional `worker_affinity` parameter
allows specifying particular worker processes
where the location content handler should be executed.
Each worker process set is represented by a bitmask of allowed worker processes.
The `all` mask allows the handler to be executed
in all worker processes.

Example:
```
example.conf:

location @periodics {
    # to be run at 1 minute intervals in worker process 0
    js_periodic main.handler interval=60s;

    # to be run at 1 minute intervals in all worker processes
    js_periodic main.handler interval=60s worker_affinity=all;

    # to be run at 1 minute intervals in worker processes 1 and 3
    js_periodic main.handler interval=60s worker_affinity=0101;

    resolver 10.0.0.1;
    js_fetch_trusted_certificate /path/to/ISRG_Root_X1.pem;
}

example.js:

async function handler(s) {
    let reply = await ngx.fetch('https://nginx.org/en/docs/njs/');
    let body = await reply.text();

    ngx.log(ngx.INFO, body);
}
```

## `js_preload_object`

**Syntax:** *`name.json`* | *`name`* from *`file.json`*

**Contexts:** `http, server, location`

Preloads an
[immutable object](https://nginx.org/en/docs/njs/preload_objects.html)
at configure time.
The `name` is used as a name of the global variable
though which the object is available in njs code.
If the `name` is not specified,
the file name will be used instead.
```
js_preload_object map.json;
```
Here, the `map` is used as a name
while accessing the preloaded object.

Several `js_preload_object` directives can be specified.

## `js_set`

**Syntax:** *`$variable`* *`function`* | *`module.function`* [`nocache`]

**Contexts:** `http, server, location`

Sets an njs `function`
for the specified `variable`.
Since [0.4.0](https://nginx.org/en/docs/njs/changes.html#njs0.4.0),
a module function can be referenced.

The function is called when
the variable is referenced for the first time for a given request.
The exact moment depends on a
[phase](https://nginx.org/en/docs/dev/development_guide.html#http_phases)
at which the variable is referenced.
This can be used to perform some logic
not related to variable evaluation.
For example, if the variable is referenced only in the
[`log_format`](https://nginx.org/en/docs/http/ngx_http_log_module.html#log_format) directive,
its handler will not be executed until the log phase.
This handler can be used to do some cleanup
right before the request is freed.

Since [0.8.6](https://nginx.org/en/docs/njs/changes.html#njs0.8.6),
if an optional argument `nocache` is specified,
the handler is called every time it is referenced.
Due to current limitations
of the [rewrite](https://nginx.org/en/docs/http/ngx_http_rewrite_module.html) module,
when a `nocache` variable is referenced by the
[set](https://nginx.org/en/docs/http/ngx_http_rewrite_module.html#set) directive
its handler should always return a fixed-length value.

> As the `js_set` handler
> returns its result immediately, it supports
> only synchronous operations.
> Thus, asynchronous operations such as
> [r.subrequest()](https://nginx.org/en/docs/njs/reference.html#r_subrequest)
> or
> [setTimeout()](https://nginx.org/en/docs/njs/reference.html#settimeout)
> are not supported.

> The directive can be specified on the
> `server` and `location` level
> since [0.7.7](https://nginx.org/en/docs/njs/changes.html#njs0.7.7).

## `js_shared_dict_zone`

**Syntax:** `zone`=*`name`*:*`size`* [`timeout`=*`time`*] [`type`=`string`|`number`] [`evict`]

**Contexts:** `http`

Sets the *`name`* and *`size`* of the shared memory zone
that keeps the
key-value [dictionary](https://nginx.org/en/docs/njs/reference.html#dict)
shared between worker processes.

By default the shared dictionary uses a string as a key and a value.
The optional `type` parameter
allows redefining the value type to number.

The optional `timeout` parameter sets
the time in milliseconds
after which all shared dictionary entries are removed from the zone.
If some entries require a different removal time, it can be set
with the `timeout` argument of the
[add](https://nginx.org/en/docs/njs/reference.html#dict_add),
[incr](https://nginx.org/en/docs/njs/reference.html#dict_incr), and
[set](https://nginx.org/en/docs/njs/reference.html#dict_set)
methods
([0.8.5](https://nginx.org/en/docs/njs/changes.html#njs0.8.5)).

The optional `evict` parameter removes the oldest
key-value pair when the zone storage is exhausted.

Example:
```
example.conf:
    # Creates a 1Mb dictionary with string values,
    # removes key-value pairs after 60 seconds of inactivity:
    js_shared_dict_zone zone=foo:1M timeout=60s;

    # Creates a 512Kb dictionary with string values,
    # forcibly removes oldest key-value pairs when the zone is exhausted:
    js_shared_dict_zone zone=bar:512K timeout=30s evict;

    # Creates a 32Kb permanent dictionary with number values:
    js_shared_dict_zone zone=num:32k type=number;

example.js:
    function get(r) {
        r.return(200, ngx.shared.foo.get(r.args.key));
    }

    function set(r) {
        r.return(200, ngx.shared.foo.set(r.args.key, r.args.value));
    }

    function del(r) {
        r.return(200, ngx.shared.bar.delete(r.args.key));
    }

    function increment(r) {
        r.return(200, ngx.shared.num.incr(r.args.key, 2));
    }
```

## `js_var`

**Syntax:** *`$variable`* [*`value`*]

**Contexts:** `http, server, location`

Declares
a [writable](https://nginx.org/en/docs/njs/reference.html#r_variables)
variable.
The value can contain text, variables, and their combination.
The variable is not overwritten after a redirect
unlike variables created with the
[`set`](https://nginx.org/en/docs/http/ngx_http_rewrite_module.html#set) directive.

> The directive can be specified on the
> `server` and `location` level
> since [0.7.7](https://nginx.org/en/docs/njs/changes.html#njs0.7.7).

