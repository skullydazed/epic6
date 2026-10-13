# usermode

Return EPIC's cached user modes for your own nickname on a server.

## Synopsis

```irc
$usermode([<server refnum>])
```

## Technical

With no arguments, the function uses the current execution server, normally the current window's server. With an argument, it uses the first word as an integer server reference number; additional words are ignored. `-1` selects the current execution server when one exists. Other negative references and nonexistent server references return an empty string.

Server names and descriptions are not resolved. The argument is parsed as a base-zero integer, so a nonnumeric name becomes `0` and can accidentally select server zero. Use [winserv](winserv.md) to obtain the server reference for a window.

The result contains the cached mode letters without a leading `+`, sorted by character value and without duplicates. It concerns your own user modes, rather than channel modes or another nickname's modes. No IRC request is sent, and no connection-state check is made; the result is the stored value for the selected server entry. Mode meanings depend on the IRC server.

## Practical

This is useful for examining modes associated with a hidden window without switching to it. The status bar's `status_umode` field uses the same cache for its window's server, but applies status formatting and display conditions and copies at most 19 mode letters; the function returns the cached string directly.

## Returns

The cached user-mode letters. An invalid server or a server with no cached modes returns an empty string.

## Examples

```irc
$usermode()              /* Modes for the current execution server. */
$usermode(2)             /* Modes for server reference 2. */
$usermode($winserv(3))    /* Modes for the server attached to window 3. */
```

## Source

Registered and implemented as `function_umode` in [source/functions.c](../../source/functions.c). Server lookup and mode storage use `get_server`, `get_server_umode`, and `update_server_umode` in [source/server.c](../../source/server.c). Status formatting uses `status_umode` in [source/status.c](../../source/status.c).
