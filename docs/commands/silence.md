# silence

## Synopsis

```text
/silence
/silence +<nick!user@host-pattern>
/silence -<nick!user@host-pattern>
/silence <nickname>
```

## Description

`silence` requests server-side message filtering on an IRC server that supports the `SILENCE` extension. Its purpose complements client-side [ignore rules](../../script/ignore): the server can stop selected messages before they reach EPIC, rather than the client receiving and hiding them.

The exact messages filtered, mask syntax, visibility of lists, limits, and availability depend on the server. Do not assume channel messages or every kind of event will be blocked. For example, [UnrealIRCd's command documentation](https://www.unrealircd.org/docs/User_%26_Oper_commands) describes filtering private messages while leaving channel messages visible.

## Options

The conventional forms are:

| Argument | Request |
| --- | --- |
| No argument | Display your current silence list. |
| `+pattern` | Add a mask to your silence list. |
| `-pattern` | Remove a mask from your silence list. |
| `nickname` | Query another user's silence list, where the server permits this. |

A full mask commonly uses `nick!user@host` with wildcards, resembling a [mode](mode.md) channel ban mask. EPIC does not require a wildcard, expand a nickname into a mask, validate masks, or enforce list limits. Some servers accept nickname forms or additional filtering flags; follow your network's syntax.

The built-in forwards `SILENCE` and the complete argument text to the current server, or sends bare `SILENCE` when no arguments are given. It uses the primary server if there is no current server context. There are no locally interpreted options.

## Output and restrictions

Server replies determine whether a change was accepted. EPIC handles incoming `SILENCE +mask` and `SILENCE -mask` messages through [on](on.md) `silence`; unless a handler suppresses display, it prints a message describing whether messages from that mask will be received. A conventional numeric 271 list entry is displayed as `user is ignoring mask`, subject to numeric and `on numeric` hooks. Other replies use ordinary numeric handling.

A server connection is required. A closed selected server normally produces a connection diagnostic; if no server entry is available, nothing is sent. Unsupported servers may report an unknown command. Normal outgoing encoding and `on send_to_server` handling apply.

The command has no script return value. EPIC does not maintain or save the server's silence list or automatically restore these entries after reconnecting. The server determines their lifetime; verify or reapply them when necessary. Server-side filtering is not a substitute for the finer control that client scripts can provide.

## Examples

```text
/silence
/silence +*!*jerk@*.example.net
/silence -*!*jerk@*.example.net
/silence foobar
```

These query your list, add and remove the same mask, and request `foobar`'s list. The last form is available only where the server allows that query.

## Source

Registration and the shared `send_comm()` handler are in [source/commands.c](../../source/commands.c). Server selection and sending are in [source/server.c](../../source/server.c). Incoming `SILENCE` acknowledgments are handled by `p_silence()` in [source/parse.c](../../source/parse.c), numeric list output is in [source/numbers.c](../../source/numbers.c), and hook definitions are in [source/hook.c](../../source/hook.c).
