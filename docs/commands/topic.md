# topic

## Synopsis

```text
/topic
/topic <channel>
/topic -
/topic -<channel>
/topic <new-topic>
/topic <channel> <new-topic>
/topic * [<new-topic>]
```

## Description

An IRC channel's topic is text describing the channel, commonly shown to users when they join. `topic` queries, changes, or clears it by sending a request to the server.

With no arguments, it queries the current window's channel. An explicit channel queries that channel. A non-channel first word instead starts a new topic for the current channel. `*` stands for the current channel and can be followed by new topic text.

A leading `-` is removed before argument parsing and requests an empty topic when no topic text follows: `/topic -` clears the current channel's topic, and `/topic -#channel` clears the named channel's topic. This sends an empty trailing IRC parameter. If text follows a channel in the hyphen form, EPIC sends that text as the new topic rather than forcing it empty.

The first word is parsed using whitespace, without removing quotes. Channel recognition uses the server's advertised `CHANTYPES`, or EPIC's default channel prefixes when unavailable. The remaining topic text is preserved. To set text beginning with a channel prefix or a hyphen, supply the channel explicitly so the text is not mistaken for target or clearing syntax. Quotes around a topic are literal text, not delimiters stripped by this command.

## Requests, replies, and restrictions

Queries send `TOPIC channel`; changes send `TOPIC channel :text`, applying the channel's outbound [encoding](encoding.md) rules to the topic. EPIC adds the trailing-parameter colon itself. If a current-channel form is used without a current channel, it prints `You are not on a channel in this window.` and sends nothing. An explicit channel does not require a current window channel.

The server decides whether you may query or change the topic. Channel mode `+t` conventionally limits changes and clearing to channel operators; additional membership, privilege, or visibility policies can apply. Topic length limits are server-dependent. EPIC does not enforce a fixed character limit or guarantee that a request is accepted; the sending layer can also truncate an overlong IRC line.

Replies arrive asynchronously. Numeric 332 is displayed as `Topic for channel: text`; numeric 333, when supplied, displays who set the topic and how many seconds ago, based on the server's timestamp and the client's clock. Numeric 331 conventionally reports that no topic is set and uses generic numeric display. Numeric-specific [on](on.md) handlers and `on numeric` can customize or suppress these replies.

Incoming `TOPIC` change messages are offered to `on topic` with sender, channel, and topic text. Unless suppressed, EPIC displays who changed the topic. This command does not return a topic string, optimistically update a local topic value, or print a local success confirmation.

Sending uses the current server context, falling back to the primary server when no current context exists. A closed selected connection normally produces a diagnostic through the `disconnect` hook; with no server record, nothing is sent. Normal outgoing mangling and `on send_to_server` handling apply. There are no client-side options beyond the syntax above, and the built-in is always registered.

## Examples

```text
/topic #blah Welcome to #blah, a very serious channel.
/topic Welcome to #blah, a very serious channel.
/topic #blah
/topic
/topic -#blah
/topic -
/topic #blah - New discussion starts here
```

These set, query, or clear a topic. Current-channel forms require a current channel; the final example preserves the leading hyphen in the topic text.

## Source

Registration and `e_topic()` are in [source/commands.c](../../source/commands.c). Channel recognition and incoming change handling are in [source/parse.c](../../source/parse.c); numeric replies are in [source/numbers.c](../../source/numbers.c). Topic recoding is in [source/recode.c](../../source/recode.c), and connection checks and outgoing formatting are in [source/server.c](../../source/server.c). Conventional query, change, and clearing semantics are described in [RFC 2812, section 3.2.4](https://www.rfc-editor.org/rfc/rfc2812#section-3.2.4).
