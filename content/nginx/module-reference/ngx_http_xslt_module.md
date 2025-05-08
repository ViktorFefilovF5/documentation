---
title: "ngx_http_xslt_module"
id: "/en/docs/http/ngx_http_xslt_module.html"
toc: true
---

## `xml_entities`

**Syntax:** *`path`*

**Contexts:** `http, server, location`

Specifies the DTD file that declares character entities.
This file is compiled at the configuration stage.
For technical reasons, the module is unable to use the
external subset declared in the processed XML, so it is
ignored and a specially defined file is used instead.
This file should not describe the XML structure.
It is enough to declare just the required character entities, for example:
```
<!ENTITY nbsp "&#xa0;">
```

## `xslt_last_modified`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

Allows preserving the "Last-Modified" header field
from the original response during XSLT transformations
to facilitate response caching.

By default, the header field is removed as contents of the response
are modified during transformations and may contain dynamically generated
elements or parts that are changed independently of the original response.

## `xslt_param`

**Syntax:** *`parameter`* *`value`*

**Contexts:** `http, server, location`

Defines the parameters for XSLT stylesheets.
The *`value`* is treated as an XPath expression.
The *`value`* can contain variables.
To pass a string value to a stylesheet,
the [`xslt_string_param`](https://nginx.org/en/docs/http/ngx_http_xslt_module.html#xslt_string_param) directive can be used.

There could be several `xslt_param` directives.
These directives are inherited from the previous configuration level
if and only if there are no `xslt_param` and
[`xslt_string_param`](https://nginx.org/en/docs/http/ngx_http_xslt_module.html#xslt_string_param) directives
defined on the current level.

## `xslt_string_param`

**Syntax:** *`parameter`* *`value`*

**Contexts:** `http, server, location`

Defines the string parameters for XSLT stylesheets.
XPath expressions in the *`value`* are not interpreted.
The *`value`* can contain variables.

There could be several `xslt_string_param` directives.
These directives are inherited from the previous configuration level
if and only if there are no [`xslt_param`](https://nginx.org/en/docs/http/ngx_http_xslt_module.html#xslt_param) and
`xslt_string_param` directives
defined on the current level.

## `xslt_stylesheet`

**Syntax:** *`stylesheet`* [*`parameter`*=*`value`* ...]

**Contexts:** `location`

Defines the XSLT stylesheet and its optional parameters.
A stylesheet is compiled at the configuration stage.

Parameters can either be specified separately, or grouped in a
single line using the “`:`” delimiter.
If a parameter includes the “`:`” character,
it should be escaped as “`%3A`”.
Also, `libxslt` requires to enclose parameters
that contain non-alphanumeric characters into single or double quotes,
for example:
```
param1='http%3A//www.example.com':param2=value2
```

The parameters description can contain variables, for example,
the whole line of parameters can be taken from a single variable:
```
location / {
    xslt_stylesheet /site/xslt/one.xslt
                    $arg_xslt_params
                    param1='$value1':param2=value2
                    param3=value3;
}
```

It is possible to specify several stylesheets.
They will be applied sequentially in the specified order.

## `xslt_types`

**Syntax:** *`mime-type`* ...

**Default:** text/xml

**Contexts:** `http, server, location`

Enables transformations in responses with the specified MIME types
in addition to “`text/xml`”.
The special value “`*`” matches any MIME type (0.8.29).
If the transformation result is an HTML response, its MIME type
is changed to “`text/html`”.

