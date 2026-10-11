# $connect()

## Synopsis

`$connect([<host> <port> [<family>]])`

## Technical

This function is retained as a compatibility placeholder. Its current implementation always returns an empty string. It does not parse or validate its arguments, resolve a hostname, open a TCP connection, or generate connection hooks or error messages.

The historical syntax accepted a host, port, and optional address family such as `4`, `6`, `v4`, or `v6`. Those arguments have no effect in the current implementation.

## Practical

Existing scripts may still call `$connect()`, but they cannot obtain a usable connection identifier from it. The former connection and communication behavior is no longer implemented.

## Returns

Always returns an empty string, including when arguments are missing or supplied.

## Examples

| Expression | Result |
| --- | --- |
| `$connect()` | Empty string |
| `$connect(127.0.0.1 25)` | Empty string |
| `$connect(example.org 443 v6)` | Empty string |

These calls do not attempt a connection.

## History

This function first appeared in ircII-2.2pre3. It is now a compatibility placeholder.

## Source

Registered and implemented in [source/functions.c](../../source/functions.c). The former connection code is disabled with `#if 0`; the active branch returns an empty string unconditionally.
