---
title: "ngx_http_split_clients_module"
id: "/en/docs/http/ngx_http_split_clients_module.html"
toc: true
---

## `split_clients`

**Syntax:** *`string`* *`$variable`* `{...}`

**Contexts:** `http`

Creates a variable for A/B testing, for example:
```
split_clients "${remote_addr}AAA" $variant {
               0.5%               .one;
               2.0%               .two;
               *                  "";
}
```
The value of the original string is hashed using MurmurHash2.
In the example given, hash values from 0 to 21474835 (0.5%)
correspond to the
value `".one"` of the `$variant` variable,
hash values from 21474836 to 107374180 (2%) correspond to
the value `".two"`,
and hash values from 107374181 to 4294967295 correspond to
the value `""` (an empty string).

