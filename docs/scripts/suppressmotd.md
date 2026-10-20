# suppressmotd script

## Synopsis

```text
/load suppressmotd
/set suppress_server_motd on|off|toggle
```

## Description

This script implements the historical `suppress_server_motd` setting. The default is `off`, explicitly reapplied when the script loads. When enabled before the server's first MOTD sequence, it attempts to hide that sequence while leaving a later [motd](../commands/motd.md) request visible.

It affects local display only. It does not prevent the server from sending its message, alter registration, or request an MOTD itself. It is separate from the local installation's [motd script](motd_script.md).

With suppression off, the script prints the intercepted MOTD numeric arguments with the normal message prefix. This is replacement output from the script rather than a promise that the client's untouched numeric formatting is retained.

## Tracking and limits

A negative-serial `375` hook starts tracking the first MOTD for a server connection. Serial-zero hooks for `372`, `375`, `376`, and `377` suppress their normal display; while tracking is active and the setting is on, they print nothing. Otherwise they print the reply arguments after the server-name field. A positive-serial `376` hook then clears the active flag and marks that first MOTD complete.

Tracking advances even when suppression is off. Once a complete first sequence has been observed, turning suppression on does not hide later MOTDs on that connection. Turning it off during a sequence allows subsequent intercepted lines to display, but does not reset the first-sequence tracking.

The server must use recognizable numeric framing. A body without the initial `375` may display; a missing `376` can leave tracking active and cause later lines to remain suppressed. Numeric `422` and other nonstandard or notice-based MOTDs are not handled by these four display hooks. Remote MOTDs are keyed by the local connection receiving them, not separately by the remote server named in their contents.

The script uses `lastserver` to key its ordinary `doing_motd` and `done_motd` variables. Its server-lost hook clears the completed flag, but not the active flag. An interrupted sequence can therefore leave stale active state across reconnection. Refnum reuse, manual state changes, or other scripts affecting these variables can also change what is considered the first MOTD.

The script allocates negative and positive serials and installs broad serial-zero numeric hooks. Other hooks can halt processing, change output, or replace the same patterns, so suppression is best-effort rather than guaranteed. Loading again resets the setting to off but does not explicitly clear the tracking variables or remove previously allocated tracking hooks.

## Requirements

The script loads [addset](addset.md) to create its Boolean setting. The `lastserver` helper from [builtins](builtins.md) must already be available; that helper returns the last server processed by the client. There is no external program or optional platform requirement.

The setting is script-provided, not a built-in MOTD policy switch. There are no additional commands, function return values, or status messages confirming that suppression occurred. The script declares no package and provides no automatic hook-removal command.

## Example

In startup configuration, before the initial server MOTD arrives:

```text
load suppressmotd
set suppress_server_motd on
```

After the first normal MOTD sequence ends, `/motd` can be used to request another copy; the script normally displays that later response. Server support and the MOTD returned still depend on the network.

## History

The retained source is marked as 2003 work by hop, restoring a former client setting through script hooks.

## Source references

- [script/suppressmotd](../../script/suppressmotd): setting default, first-sequence flags, numeric display, and server-lost cleanup.
- [script/addset](../../script/addset) and [script/builtins](../../script/builtins): setting creation and `lastserver` helper.
- [source/functions.c](../../source/functions.c): `getserial` obtains unused hook serials.
- [source/hook.c](../../source/hook.c): serial ordering, free-serial selection, and serial-zero suppression.
- [source/numbers.c](../../source/numbers.c): generic numeric arguments and default-display path.
- [source/server.c](../../source/server.c): last-server state and server-lost dispatch.
