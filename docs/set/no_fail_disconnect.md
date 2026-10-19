# set no_fail_disconnect

## Synopsis

```text
/set no_fail_disconnect [on|off|toggle]
```

Default: `off`. With no argument, [set](../commands/set.md) displays the current value.

## Description

When on, prevents the normal server-write routine from automatically resetting an established connection when its write call returns `-1`. This can be useful when experimenting with a temporarily interrupted network connection that might recover without replacing the TCP connection.

With the setting off, a failed write on a registered server causes EPIC to display `Write to server failed. Resetting connection.`, mark the server as being in error, run [on reconnect_required](../on/reconnect_required.md) with the server refnum, and close the connection. With it on, that particular error-handling sequence is skipped. This setting is global and applies to subsequent writes; changing it does not itself connect, disconnect, or retry anything.

## Limitations

This controls one write-error path, not every cause of disconnection or reconnection. Read errors and end-of-file processing can still close the connection and invoke reconnection handling. The write-error reset path is limited to servers EPIC regards as registered, including its syncing and active states.

The raw send routine uses either the plain socket writer or the TLS writer, then checks whether its result is exactly `-1`. The setting does not change those writers' return conventions, handle partial writes, or queue and retransmit data from failed writes. It is not a guarantee that messages were delivered.

A static IP address alone does not guarantee that an IRC connection survives a network outage. The operating system, network equipment, and IRC server determine whether the existing connection remains usable; this setting cannot preserve a connection that has actually been lost.

## Example

Skip this particular automatic reset after a failed server write:

```text
/set no_fail_disconnect on
```

Restore the default write-error handling with `/set no_fail_disconnect off`.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): boolean registration and off default.
- [source/server.c](../../source/server.c): `send_to_aserver_raw` tests the write result and this setting; `is_server_registered` defines the eligible states; server read-error handling is separate.
- [source/ssl.c](../../source/ssl.c): TLS write wrapper used by the raw send routine.
