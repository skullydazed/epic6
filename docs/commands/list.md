# list

Request a channel directory from the IRC server.

## Synopsis

```text
/list [server-specific arguments]
/list channel
```

## Description

`list` asks the server for channel information, normally including each channel's name, user count, and topic. With no arguments, EPIC sends `LIST`. With arguments, it appends the entire supplied text unchanged. The request uses the server in the current command context, falling back to the primary server when there is no server context.

A channel name conventionally requests information about that channel. Wildcards, channel lists, user-count filters, and other query forms depend on the server. The built-in does not substitute the current channel for `*`, parse filtering switches, or sort the results. Supply an explicit channel name when that is what you want.

Replies arrive asynchronously, one entry per line. EPIC offers the header and entries to the `list` hook with channel, user count, and topic as arguments. The header's values are `Channel Users Topic`. Numeric hooks, including `321` and `322`, and the general numeric hook can also suppress their normal display. The end-of-list reply is displayed through ordinary numeric handling. See [on](on.md).

## Display and filtering

`/set channel_name_width` controls channel-name formatting: zero, the default, uses the full name followed by a tab; a positive width pads and truncates the name to that width. User counts occupy a minimum-width column, followed by the topic. A server entry whose channel name begins with `*` is displayed as `Prv` rather than revealing a channel name. Entries are not specifically routed into that channel's window.

There are no client-side `-min`, `-max`, `-public`, `-private`, `-topic`, `-wide`, `-name`, or `-users` options in this built-in. Such text is forwarded to the server, whose interpretation may differ or reject it. Scripts can override the command or filter display with hooks. Display filtering still requires the client to receive the underlying replies; server-side filters can reduce the data sent when supported.

## Restrictions

The server determines which channels are visible, including how private or secret channels and your own memberships affect the directory. It can restrict, limit, or reject queries. A large unfiltered list can produce substantial output; prefer server-supported filters when appropriate and do not wait indefinitely for a reply in a script.

Sending requires a usable connection. A closed connection normally produces a diagnostic subject to the `disconnect` hook; with no server record, no request is sent. The built-in is always registered.

## Examples

Request the server's channel directory:

```text
/list
```

Request one channel, if supported by the server:

```text
/list #epic
```

Consult your server's supported `LIST` syntax before supplying filters or wildcard arguments.

## Source

Registration and the shared `send_comm` handler are in [source/commands.c](../../source/commands.c); there is no special `list` branch. Server selection and transmission are in [source/server.c](../../source/server.c). Reply hooks and formatting are in [source/numbers.c](../../source/numbers.c), with `channel_name_width` registered in [source/vars.c](../../source/vars.c) and its default in [include/config.h](../../include/config.h).
