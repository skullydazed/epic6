# pretend

Process a supplied IRC line as though it had arrived from the current server.

## Synopsis

```text
/pretend irc-line
```

## Description

`pretend` lets a script replace or synthesize an incoming server message. It is primarily useful inside a [raw_irc hook](on.md), where a script can suppress the original message and feed an altered line to EPIC's normal IRC parser. It is an advanced scripting tool, rather than a display-only formatter.

All argument text is treated as one IRC protocol line, including any sender prefix, command or numeric, parameters, and trailing `:text`. Named protocol commands must use their wire spelling, such as `PRIVMSG`. There are no options or separate server argument. Normal script expansion occurs before the handler receives the line.

The current server context must identify an open connection; otherwise the command reports `Server reference is not connected`. It does not fall back to the primary server when that context is absent. The built-in is always registered. It does not enforce hook-only use, but use outside a hook must still provide an appropriate connected-server context.

An empty line produces no parser action after the connection check. Input is truncated to at most 8191 bytes in the current build. Malformed or unrecognized protocol messages follow the parser's ordinary protocol-error handling; there is no separate pretend success message or result value.

## Hooks and effects

The injected line passes through `raw_irc`, inbound line mangling, and the normal command or numeric handler. `raw_irc` is nonrecursive by default, so an injection from within that same hook does not ordinarily invoke it again. Other downstream hooks can run normally. The command restores its original server context after parsing.

This path bypasses the network receive layer's `raw_irc_bytes` hook and initial conversion of incoming bytes to UTF-8. Supply normal internal UTF-8 text rather than raw bytes in a server's external encoding.

Injected events can modify EPIC's actual tracked state: joins, parts, nickname changes, modes, and registration numerics can affect channels, members, and the session. They can also trigger real outgoing messages through protocol handlers or scripts; for example, an injected server `PING` can cause a `PONG`. Use such state-changing messages only when they reflect events that actually occurred, or when you deliberately intend those effects. Suppressing a downstream display hook does not generally prevent state changes.

## Example

Replace one specific incoming private-message text before ordinary message handling:

```text
/on ^raw_irc "% PRIVMSG % :hello" {
    pretend :$0 PRIVMSG $2 :(rewritten) hello
}
```

For a matching prefixed line, `$0` is the sender prefix and `$2` the recipient. The silent serial-zero hook suppresses processing of the original line; the injected line then follows normal private-message handling. The leading colon in `:$0` restores the prefix marker omitted from the `raw_irc` event data.

## Source

Registration and `pretend_cmd` are in [source/commands.c](../../source/commands.c). `parse_server`, tokenization, and protocol handlers are in [source/parse.c](../../source/parse.c). Normal network preprocessing and connection checks are in [source/server.c](../../source/server.c), hook recursion policy in [source/hook.c](../../source/hook.c), and the input-buffer size in [include/newio.h](../../include/newio.h).
