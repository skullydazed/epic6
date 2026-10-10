# invite

Ask an IRC user to join a channel.

## Synopsis

```text
/invite nickname [channel]
/invite channel nickname [nickname ...]
/invite nickname channel [channel ...]
```

## Description

`invite` sends an IRC `INVITE nickname channel` request through the server in the current command context. A nickname without an explicit channel uses the current window's channel. Channel-first syntax can invite several nicknames to the same channel; nickname-first syntax can invite one nickname to several channels. EPIC sends a separate request for each pair.

The server decides whether the invitation is allowed. Invite-only channels commonly require channel-operator privileges, and servers may also require membership or impose other restrictions. An invitation does not automatically make the recipient join. Success and error replies arrive asynchronously.

## Arguments and parsing

Arguments are whitespace-delimited. EPIC recognizes channel names using the current server's `CHANTYPES` information, falling back to its standard channel prefixes. Other arguments are treated as nicknames. `*` stands for the current window's channel.

The parser retains the most recent nickname and channel, and sends a request after each new argument once both are available. This supports the forms above, but is not pairwise parsing: mixing several nicknames and channels in one invocation can issue intermediate or repeated invitations. Use separate commands for independent pairs. Comma-separated lists are not split by the client.

With a nickname alone and no current channel, EPIC reports that you are not on a channel and prints usage. Supplying only a channel or no arguments also prints usage. If you explicitly supply `*` without a current channel, EPIC forwards `*` as the channel instead of issuing that local missing-channel diagnostic; avoid this form.

There are no client-side options, and the built-in is always registered. The sending layer requires a usable server connection. A closed connection normally produces a not-connected diagnostic, subject to the `disconnect` hook; with no server record, no request is sent. EPIC does not check membership or operator privileges before forwarding invitations.

## Examples

Invite `foobar` to `#blah`:

```text
/invite foobar #blah
```

Invite two people to the same channel:

```text
/invite #blah foobar joebob
```

Invite someone to the current channel:

```text
/invite foobar
```

Join the last channel to which you received an invitation:

```text
/join -invite
```

That last example uses [join](join.md), not an `invite` option. Received invitations are stored per server and displayed subject to the `invite` hook.

## Source

Registration and `send_invite` are in [source/commands.c](../../source/commands.c). Channel recognition and incoming invitations are in [source/parse.c](../../source/parse.c); connection checks and the saved invitation channel are in [source/server.c](../../source/server.c). The `join -invite` behavior is in [source/window.c](../../source/window.c).
