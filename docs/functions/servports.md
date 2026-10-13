# $servports()

## Synopsis

`$servports([server refnum])`

## Technical

Returns the remote and local port assignments for the specified server connection, separated by one space. With an open socket, these are the ports recorded in the remote and local socket addresses.

Without an argument, the function uses the current execution server. Explicit `-1` also selects that server. Only the first integer argument is used, and additional arguments are ignored. Server names and descriptions are not resolved: use [$serverctl(refnum ...)](serverctl.md) first. Integer parsing is permissive, so nonnumeric input becomes zero.

If the server entry exists but its socket is closed, the remote value is its configured connection port, falling back to the client's default IRC port when unset; the local value is `0`. An open socket does not necessarily mean IRC registration is complete.

## Returns

A string containing two decimal integers, in remote-then-local order.

Negative refnums remaining after the `-1` substitution, or values greater than the server-list slot bound, return empty. A missing entry inside that bound returns `0 0`. The implementation also permits the refnum equal to the bound, even though that is beyond the last slot, and returns `0 0` for it. Thus an invalid refnum does not consistently produce one error result.

No options, state changes, or network requests are performed.

## Examples

`$servports(0)` could return `6667 1054`, where `6667` is the server's port and `1054` is the client's local port.

For an existing closed server configured to use port `6697`, `$servports(<refnum>)` returns `6697 0`.

`$servports(6127)` returns empty if `6127` is greater than the server-list slot bound; it does not substitute the default IRC port for every invalid refnum.

`$servports()` and `$servports(-1)` query the current execution server.

Registration and `function_servports()`: [source/functions.c](../../source/functions.c). Slot bound, port lookup, and socket-state checks: [source/server.c](../../source/server.c). Integer parsing: [include/functions.h](../../include/functions.h).
