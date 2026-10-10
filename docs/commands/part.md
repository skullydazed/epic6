# part

Leave one or more IRC channels.

## Synopsis

```text
/part [channel[,channel...]|*] [part-message]
```

## Description

When you wish to leave a channel, you part it. Once the server processes the departure, you are no longer a member, lose any channel-operator or voice privileges there, and stop receiving its ordinary channel messages. Other members normally receive a departure notification, optionally including your part message. Server policy determines whether the request and message are accepted or altered.

The first whitespace-delimited argument is the channel target. A missing target or the exact token `*` selects the current window's current channel. This selects a channel, not a query nickname. With no current channel, EPIC sends literal `*` and lets the server respond; it does not report a local missing-channel error.

A comma-separated list is forwarded as one target token. The built-in does not split or validate it, and does not expand `*` inside such a list. Use explicit channel names for multiple channels. Scripts may provide aliases with additional target handling; that behavior is separate from this built-in.

All remaining argument text is the optional part message, sent as one trailing IRC parameter. There is no default message. To give a reason for leaving the current channel, write `/part * reason`; `/part reason` treats `reason` as the channel target. Destination [encoding](encoding.md) applies to the reason, and the server sending layer can truncate long requests rather than split them.

## Output and restrictions

The command sends a request without changing local channel membership immediately or producing a success message. When an incoming `PART` confirms your departure, EPIC removes the channel from its tracked state. For another user's departure it removes that member instead. The `part` [on](on.md) hook receives the nickname, channel, userhost, and optional reason; suppressing its notification output does not prevent the state update.

The built-in is always registered and has no options or client-side membership checks. It requires a usable IRC connection, using the current server context or the primary server as fallback when no context exists. Server errors are handled through ordinary numeric replies and hooks. The outgoing `send_to_server` hook can suppress transmission.

## Examples

Leave `#foobar` with a reason:

```text
/part #foobar I'm going to #fubar, it's cooler!
```

Leave the current channel:

```text
/part *
```

Leave two explicit channels:

```text
/part #foo,#bar
```

See [join](join.md) to enter a channel again.

## Source

Registration and `send_2comm` target/reason handling are in [source/commands.c](../../source/commands.c). Incoming departure hooks and state updates are in [source/parse.c](../../source/parse.c), channel removal in [source/names.c](../../source/names.c), current-channel lookup in [source/window.c](../../source/window.c), and transmission in [source/server.c](../../source/server.c).
