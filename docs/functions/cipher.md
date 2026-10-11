# $cipher()

## Synopsis

`$cipher([<refnum>])`

## Technical

Returns the cipher name reported by OpenSSL for the TLS connection associated with server `<refnum>`. This reports connection information; it does not select or change a cipher.

Omitting `<refnum>` uses the server in the current execution context. An explicit argument is a numeric server reference number, not a server name. Negative reference numbers, including `-1`, return an empty string rather than selecting the current server. Additional arguments are ignored.

## Returns

Returns the result of `SSL_get_cipher_name()` for the connection's TLS session. Returns an empty string for an invalid server reference, a connection without TLS state, or missing TLS session state. Before a cipher has been negotiated, the value is whatever OpenSSL reports for that session.

## Restrictions

The function is always registered in current EPIC builds, which require OpenSSL. A cipher name alone does not report certificate verification or prove the identity of the remote server.

## Examples

```irc
 echo Current connection cipher: $cipher()
 echo Server 0 cipher: $cipher(0)
```

The second example returns an empty string if server 0 has no TLS connection state.

## History

The `$cipher()` function first appeared in EPIC4-1.1.3.

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). Server connection lookup is in [source/server.c](../../source/server.c), and the OpenSSL query is in [source/ssl.c](../../source/ssl.c). The OpenSSL build requirement is in [configure.ac](../../configure.ac).
