# set ssl_ciphers

## Synopsis

```text
/set ssl_ciphers [<text>]
/set -ssl_ciphers
```

Default: unset. With no value, [set](../commands/set.md) displays the current value. The hyphen form clears the string.

## Description

Supplies the cipher-list string passed to OpenSSL’s `SSL_CTX_set_cipher_list` when EPIC first creates its shared SSL context. With no configured string, EPIC leaves that choice to OpenSSL. The accepted cipher-list syntax depends on the linked SSL library.

Set this before the first SSL connection. Although a cipher-update function exists, this setting has no change callback: changing or clearing it after context creation does not update the context, even for later connections. EPIC does not report the cipher-list call’s return value through this setting.

## Example

```text
/set ssl_ciphers DEFAULT
```

## Source references

- [source/ssl.c](../../source/ssl.c): setting behavior.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): registration, callback, and default.
