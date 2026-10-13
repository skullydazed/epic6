# $ssl()

## Synopsis

`$ssl()`

## Return value

Returns `1`, indicating that the client implements SSL/TLS support. The current helper unconditionally returns `1`; there is no active no-SSL branch returning `0`. The current configure process requires OpenSSL and stops if it cannot find it.

All arguments are ignored by the C handler, although normal function-argument expansion still occurs. It does not select a server, establish a connection, or change TLS configuration.

## Practical

Use this as a client-capability query. It says nothing about whether a particular connection uses TLS or whether a certificate was accepted or verified. [$isencrypted()](isencrypted.md) reports connection TLS metadata, [$cipher()](cipher.md) queries a server's cipher, and [$serverctl()](serverctl.md) exposes connection and certificate details.

## Examples

`$ssl()` returns `1`. `$ssl(2)` also returns `1`; it does not inspect server 2. No IRC connection is required.

## History

First appeared before EPIC4-1.0, when it returned `0`. It began returning `1` in EPIC4-1.1.3.

Registration and `function_ssl()`: [source/functions.c](../../source/functions.c). `client_ssl_enabled()`: [source/ssl.c](../../source/ssl.c). OpenSSL detection and requirements: [configure.ac](../../configure.ac).
