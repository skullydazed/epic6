# admin command

## Synopsis

```text
/admin
/admin <server>
/admin <nickname>
```

## Description

This is a pass-through command. It requests information about who runs an IRC server and how you can contact them, usually by email.

Without arguments, the request queries the server you are connected to in the current command context. A server name requests information about that remote server. On servers that support a nickname as the target, a nickname requests information about that user's server. EPIC forwards the target unchanged; it does not resolve a nickname into a server name itself.

The built-in has no client-side options and does not validate the arguments. It sends `ADMIN` when no arguments are supplied, or `ADMIN` followed by the supplied text otherwise. Additional arguments are interpreted by the server, not by EPIC. The command is available in every build and requires an active server connection to send the request.

Replies arrive asynchronously and are displayed as ordinary server numeric messages. Scripts can handle or suppress them with numeric [`on`](on.md) hooks. The `show_numerics`, `banner`, and `banner_expand` settings control their normal display prefix; they do not change the request.

## Restrictions

The information returned, accepted target forms, and access restrictions depend on the server. Some servers restrict or limit remote queries or do not support nickname targets. You may receive an error message or no reply at all. A script should not wait indefinitely for a remote server reply.

If the selected server connection is closed, EPIC's sending layer normally reports that you are not connected; a `disconnect` hook can suppress that message. If no server record is available, no request is sent.

## Examples

To get administrative information for the current server:

```text
/admin
```

To query JoeBob's server, if your server supports nickname targets:

```text
/admin joebob
```

To query a named server:

```text
/admin irc.foobar.com
```

## History

The `admin` command first appeared in ircII.

## Source

See [source/commands.c](../../source/commands.c) for the `admin` registration and `send_comm()` handler; this shared handler has no special `admin` branch. [source/server.c](../../source/server.c) implements `send_to_server()` and connection checks in `vsend_to_aserver_with_payload()`. [source/numbers.c](../../source/numbers.c) implements numeric hooks, default reply display, and the display prefix; [source/vars.c](../../source/vars.c) registers the related settings.
