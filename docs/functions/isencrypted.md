# $isencrypted()

## Synopsis

`$isencrypted([<server refnum>])`

## Arguments

The optional integer server reference defaults to the current execution context's server (`from_server`). Only the first argument is used; additional arguments are ignored. Integer conversion accepts decimal, hexadecimal, and leading-zero octal. Nonnumeric text converts to zero rather than resolving a server name.

Explicit negative references, including `-1` and `-2`, return `0`; they do not select the current server or aggregate all servers. The omitted-argument default is therefore different from explicitly supplying `-1`.

## Return value

| Value | Condition |
| --- | --- |
| `1` | The selected server's socket has EPIC SSL/TLS metadata. |
| `0` | The reference is invalid or deleted, the socket is closed, or it has no SSL/TLS metadata. |

The function checks the connection's TLS setup, not per-message encryption. It sends no request and changes no state. It does not require an IRC session to be registered: TLS metadata is created when negotiation starts, so `1` can occur before the handshake, certificate checks, or IRC registration finish. It does not by itself establish certificate acceptance or peer identity.

## See also

Since `0` can mean several things, use [serverctl](serverctl.md) to inspect the server reference and its state. [isconnected](isconnected.md) separately tests whether IRC registration has completed; [cipher](cipher.md) reports the connection's cipher.

## Examples

```text
$isencrypted()
$isencrypted(2)
```

These inspect the current context's server and server reference `2`, respectively.

## History

This function was part of EPIC-SSL and first appeared in EPIC4-1.1.3. The current build requires OpenSSL; this built-in is registered unconditionally.

## Source

Registered and implemented as `function_isencrypted` in [source/functions.c](../../source/functions.c). Server/socket lookup is in [source/server.c](../../source/server.c), TLS metadata creation and `is_fd_ssl_enabled` are in [source/ssl.c](../../source/ssl.c), and the OpenSSL requirement is in [configure.ac](../../configure.ac).
