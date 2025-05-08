---
title: "ngx_mail_smtp_module"
id: "/en/docs/mail/ngx_mail_smtp_module.html"
toc: true
---

## `smtp_auth`

**Syntax:** *`method`* ...

**Default:** plain login

**Contexts:** `mail, server`

Sets permitted methods of
[SASL authentication](https://datatracker.ietf.org/doc/html/rfc2554)
for SMTP clients.
Supported methods are:
- `plain`

    [AUTH PLAIN](https://datatracker.ietf.org/doc/html/rfc4616)
- `login`

    [AUTH LOGIN](https://datatracker.ietf.org/doc/html/draft-murchison-sasl-login-00)
- `cram-md5`

    [AUTH CRAM-MD5](https://datatracker.ietf.org/doc/html/rfc2195).
    In order for this method to work, the password must be stored unencrypted.
- `external`

    [AUTH EXTERNAL](https://datatracker.ietf.org/doc/html/rfc4422) (1.11.6).
- `none`

    Authentication is not required.

Plain text authentication methods
(`AUTH PLAIN` and `AUTH LOGIN`)
are always enabled,
though if the `plain` and `login` methods
are not specified,
`AUTH PLAIN` and `AUTH LOGIN`
will not be automatically included in [`smtp_capabilities`](https://nginx.org/en/docs/mail/ngx_mail_smtp_module.html#smtp_capabilities).

## `smtp_capabilities`

**Syntax:** *`extension`* ...

**Contexts:** `mail, server`

Sets the SMTP protocol extensions list
that is passed to the client in response to the
`EHLO` command.
The authentication methods specified in the [`smtp_auth`](https://nginx.org/en/docs/mail/ngx_mail_smtp_module.html#smtp_auth) directive and
[STARTTLS](https://datatracker.ietf.org/doc/html/rfc3207)
are automatically added to this list depending on the
[`starttls`](https://nginx.org/en/docs/mail/ngx_mail_ssl_module.html#starttls) directive value.

It makes sense to specify the extensions
supported by the MTA
to which the clients are proxied (if these extensions are related to commands
used after the authentication, when nginx transparently proxies the client
connection to the backend).

The current list of standardized extensions is published at
[www.iana.org](http://www.iana.org/assignments/mail-parameters).

## `smtp_client_buffer`

**Syntax:** *`size`*

**Default:** 4k|8k

**Contexts:** `mail, server`

Sets the *`size`* of the buffer used for reading SMTP commands.
By default, the buffer size is equal to one memory page.
This is either 4K or 8K, depending on a platform.

## `smtp_greeting_delay`

**Syntax:** *`time`*

**Default:** 0

**Contexts:** `mail, server`

Allows setting a delay before sending an SMTP greeting
in order to reject clients who fail to wait for the greeting before
sending SMTP commands.

