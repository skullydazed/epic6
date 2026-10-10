# mode

Query or change IRC user and channel attributes.

## Synopsis

```text
/mode nickname-or-channel [mode-changes [parameters...]]
/mode * [mode-changes [parameters...]]
```

## About modes

Modes describe attributes of a user session or channel. They commonly use single letters: `+` sets a mode and `-` removes it. Several changes can be clustered, with required parameters following the mode string in order. The server defines the letters, parameter rules, privileges, and maximum changes per request.

## User modes

User modes apply to your connection. Servers generally let you query or change your own modes, while reserving privileged modes for server or service assignment. They may grant defaults on connection or additional modes after [oper](oper.md). Modes and permissions can be restored by server policy or services after reconnecting; EPIC does not make them persistent itself.

Common examples are `i` for restrictions on visibility in user searches, `w` for wallops reception, and `s` for server notices. Their exact effects and availability depend on the network. User mode `o` commonly denotes IRC-operator status and cannot ordinarily be obtained by setting `+o`; use the server's operator authentication procedure. Removing it with `-o` is commonly supported; see [deop](deop.md).

## Channel modes

Channel modes control membership, speaking, visibility, and privileges. Common conventions include:

| Mode | Conventional purpose |
| --- | --- |
| `b mask` | Ban mask; requesting `+b` without a mask commonly lists bans. |
| `i` | Require an invitation to join. |
| `k key` | Require a channel key. |
| `l number` | Limit membership count. |
| `m` | Restrict speaking to suitably privileged members. |
| `n` | Restrict messages from outside the channel. |
| `o nickname` | Grant channel-operator status. |
| `s` | Hide the channel from some external queries. |
| `t` | Restrict topic changes. |
| `v nickname` | Grant voice status. |
| `e mask`, `I mask` | Ban or invitation exceptions on networks supporting them. |

These are conventions, not universal definitions. Modes such as `p`, additional privilege ranks, service-controlled modes, and server-notice masks differ substantially between networks. Channel creation defaults, persistence, and who can change or query modes are also server decisions. A channel operator cannot necessarily override every privilege or protection.

## Command behavior

The first whitespace-delimited argument is the target. `*` substitutes the current window's channel, not its query nickname. Without a current channel, that form prints a diagnostic and sends nothing. With no target, the command prints usage; it does not automatically query your own modes.

All remaining text is forwarded unchanged after the target. The built-in does not validate mode letters, expand ban masks, reorder parameters, split changes into several requests, or enforce a fixed four- or six-parameter limit. With a target alone, it sends a mode query. Normal outbound server encoding and IRC line-length limits apply.

Requests use the server in the current command context, falling back to the primary server when there is no server context. A usable connection is required. A closed connection normally produces a diagnostic subject to the `disconnect` hook; with no server record, no request is sent. This built-in is always registered and has no client-side options.

## Replies and local state

Replies and confirmed changes arrive asynchronously. Numeric `221` normally displays your user modes, and `324` displays channel modes; list modes use their own numeric replies. Numeric [on](on.md) hooks can handle or suppress normal display.

Incoming changes are offered to the `mode` hook and normally displayed as a mode-change message. Suppressing that display does not prevent the client's subsequent mode-state update. `/set mode_stripper`, off by default, additionally offers individual changes through the stripped-mode hook. Channel bookkeeping uses server-advertised `PREFIX` and `CHANMODES` rules, with conventional fallbacks when absent. This incoming interpretation does not change the outgoing command's pass-through behavior.

## Examples

Query your own modes or the current channel's modes:

```text
/mode your_nick
/mode *
```

Request wallops and server notices, where those modes are supported:

```text
/mode your_nick +sw
```

Set a membership limit or grant channel-operator status:

```text
/mode #channel +l 35
/mode #channel +o theirnick
```

Cluster a ban, a key, and removal of channel-operator status:

```text
/mode #channel +bk-o *!*some@*.ban.com Secret_Key foobar
```

The final example assumes those mode meanings and parameter rules are supported by your server.

## Source

Registration and `send_channel_com` are in [source/commands.c](../../source/commands.c), with no special `mode` branch. Incoming changes and hooks are in [source/parse.c](../../source/parse.c), replies in [source/numbers.c](../../source/numbers.c), and advertised channel-mode interpretation in [source/names.c](../../source/names.c). Transmission is in [source/server.c](../../source/server.c). `mode_stripper` is registered in [source/vars.c](../../source/vars.c), with its default in [include/config.h](../../include/config.h).
