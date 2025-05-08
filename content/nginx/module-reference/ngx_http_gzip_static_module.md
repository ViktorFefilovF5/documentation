---
title: "ngx_http_gzip_static_module"
id: "/en/docs/http/ngx_http_gzip_static_module.html"
toc: true
---

## `gzip_static`

**Syntax:** `on` | `off` | `always`

**Default:** off

**Contexts:** `http, server, location`

Enables (“`on`”) or disables (“`off`”)
checking the existence of precompressed files.
The following directives are also taken into account:
[`gzip_http_version`](https://nginx.org/en/docs/http/ngx_http_gzip_module.html#gzip_http_version),
[`gzip_proxied`](https://nginx.org/en/docs/http/ngx_http_gzip_module.html#gzip_proxied),
[`gzip_disable`](https://nginx.org/en/docs/http/ngx_http_gzip_module.html#gzip_disable),
and [`gzip_vary`](https://nginx.org/en/docs/http/ngx_http_gzip_module.html#gzip_vary).

With the “`always`” value (1.3.6), gzipped file is used
in all cases, without checking if the client supports it.
It is useful if there are no uncompressed files on the disk anyway
or the [ngx_http_gunzip_module](https://nginx.org/en/docs/http/ngx_http_gunzip_module.html)
is used.

The files can be compressed using the `gzip` command,
or any other compatible one.
It is recommended that the modification date and time of original and
compressed files be the same.

