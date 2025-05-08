---
title: "ngx_mail_imap_module"
id: "/en/docs/mail/ngx_mail_imap_module.html"
toc: true
---

## `imap_auth`

**Syntax:** *`method`* ...

**Default:** plain

**Contexts:** `mail, server`

Sets permitted methods of authentication for IMAP clients.
Supported methods are:
- `plain`

    [LOGIN](https://datatracker.ietf.org/doc/html/rfc3501),
    [AUTH=PLAIN](https://datatracker.ietf.org/doc/html/rfc4616)
- `login`

    [AUTH=LOGIN](https://datatracker.ietf.org/doc/html/draft-murchison-sasl-login-00)
- `cram-md5`

    [AUTH=CRAM-MD5](https://datatracker.ietf.org/doc/html/rfc2195).
    In order for this method to work, the password must be stored unencrypted.
- `external`

    [AUTH=EXTERNAL](https://datatracker.ietf.org/doc/html/rfc4422) (1.11.6).

Plain text authentication methods
(the `LOGIN` command, `AUTH=PLAIN`,
and `AUTH=LOGIN`) are always enabled,
though if the `plain` and `login` methods
are not specified,
`AUTH=PLAIN` and `AUTH=LOGIN`
will not be automatically included in [`imap_capabilities`](https://nginx.org/en/docs/mail/ngx_mail_imap_module.html#imap_capabilities).

## `imap_capabilities`

**Syntax:** *`extension`* ...

**Default:** IMAP4 IMAP4rev1 UIDPLUS

**Contexts:** `mail, server`

Sets the
[IMAP protocol](https://datatracker.ietf.org/doc/html/rfc3501)
extensions list that is passed to the client in response to
the `CAPABILITY` command.
The authentication methods specified in the [`imap_auth`](https://nginx.org/en/docs/mail/ngx_mail_imap_module.html#imap_auth) directive and
[STARTTLS](https://datatracker.ietf.org/doc/html/rfc2595)
are automatically added to this list depending on the
[`starttls`](https://nginx.org/en/docs/mail/ngx_mail_ssl_module.html#starttls) directive value.

It makes sense to specify the extensions
supported by the IMAP backends
to which the clients are proxied (if these extensions are related to commands
used after the authentication, when nginx transparently proxies a client
connection to the backend).

The current list of standardized extensions is published at
[www.iana.org](http://www.iana.org/assignments/imap4-capabilities).

## `imap_client_buffer`

**Syntax:** *`size`*

**Default:** 4k|8k

**Contexts:** `mail, server`

Sets the *`size`* of the buffer used for reading IMAP commands.
By default, the buffer size is equal to one memory page.
This is either 4K or 8K, depending on a platform.

