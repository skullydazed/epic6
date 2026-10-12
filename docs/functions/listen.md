# $listen()

## Synopsis

```text
$listen()
$listen(<port> [<family>])
```

## Technical

Retained as a compatibility placeholder. The current built-in always returns the empty string. It does not parse or validate arguments, bind a port, create a listening socket, accept connections, or produce connection diagnostics.

The historical port and family arguments, including `4`, `6`, `v4`, and `v6`, have no effect. Omitting the port or supplying zero does not request a system-allocated port in the current implementation. There are no active port-range checks or address-family options.

## Practical

Existing scripts can still call the function, but cannot use it to act as a TCP server or obtain a listening port. The related [connect function](connect.md) is also a compatibility placeholder.

## Returns

Always the empty string, regardless of arguments.

## Examples

| Expression | Result |
| --- | --- |
| `$listen(1025)` | Empty string |
| `$listen(1023)` | Empty string |
| `$listen(0)` | Empty string |
| `$listen()` | Empty string |

None of these opens a socket or reserves a port.

## History

This function first appeared in ircII-2.2pre3. Its former passive TCP listener implementation is disabled in the current source.

## Source

Registered and implemented as `function_listen` in [source/functions.c](../../source/functions.c). The former implementation is inside `#if 0`; the active branch returns the empty string unconditionally.
