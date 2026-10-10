# names

Request channel membership names from the IRC server.

## Synopsis

```text
/names [server-specific arguments]
/names channel
```

## Description

`names` asks the server for nicknames in channels, rather than the user counts and topics returned by [list](list.md). A channel name conventionally selects that channel. With no arguments, EPIC sends `NAMES`; the breadth of that request depends on the server.

The built-in forwards all supplied text unchanged after `NAMES`. It does not replace `*` with the current channel, parse client-side filters, expand wildcard channel names, or validate targets. Channel lists, remote targets, and other supported arguments are server-dependent. There are no client-side `-min`, `-max`, `-public`, `-private`, or `-all` options in this implementation.

The request uses the server in the current command context, falling back to the primary server when there is no server context. Replies arrive asynchronously and may span several lines. Nicknames often carry status prefixes: `@` commonly means channel operator and `+` voice. Additional prefixes depend on the server's advertised `PREFIX` rules.

## Replies and local state

Numeric `353` supplies a channel and nickname list; `366` conventionally ends the response. Individual numeric hooks and the general numeric hook can suppress normal display. The `names` hook receives the channel and nickname list for each displayed reply. See [on](on.md).

During channel synchronization after joining, EPIC adds the received names to its channel state, recognizing advertised status prefixes with a conventional fallback. The join-time display normally reads `Users on channel: names` and is controlled by `/set show_channel_names`, on by default, as well as hooks. Turning that setting off does not disable all manually requested NAMES output or prevent the synchronization bookkeeping.

Outside synchronization, replies are displayed with public/private/secret labels based on the server's type marker. `/set channel_name_width` is consulted for channel-name formatting. A manual query is not a general replacement of EPIC's cached member list; the synchronization path performs the membership additions.

## Restrictions

Visibility depends on server rules, channel privacy, user modes, membership, and privileges. Invisible users or secret channels may be omitted from queries made outside a channel. A returned list should not be treated as a guaranteed complete directory of the network.

Large unrestricted requests can generate substantial output. Prefer specific channels, and do not wait indefinitely for a response in a script. Servers can restrict or rate-limit queries.

Sending requires a usable connection. A closed connection normally produces a diagnostic subject to the `disconnect` hook; with no server record, no request is sent. The built-in is always registered.

## Examples

```text
/names #epic
/names
```

The first example requests one channel. The second uses the server's no-argument behavior rather than a client-side all-channel scan.

## Source

Registration and the shared `send_comm` handler are in [source/commands.c](../../source/commands.c). Server selection and transmission are in [source/server.c](../../source/server.c). Numeric handling, hooks, synchronization checks, and formatting are in [source/numbers.c](../../source/numbers.c); member and prefix processing are in [source/names.c](../../source/names.c). Display settings are registered in [source/vars.c](../../source/vars.c), with defaults in [include/config.h](../../include/config.h).
