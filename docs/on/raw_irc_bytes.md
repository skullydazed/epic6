# on raw_irc_bytes

## Synopsis

```text
/on [<noise>]raw_irc_bytes "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for a complete incoming server line before it enters the IRC parser. Unlike [on raw_irc](raw_irc.md), this hook does not remove a leading colon or insert a `*` prefix.

Use standard [on](../commands/on.md) matching, serial numbers, and noise controls. The pattern matches the complete event data.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$*` | Contents of the receive buffer, with trailing newline and carriage return removed. |

## Behavior

A suppressing serial-zero handler prevents parsing of the line, including `raw_irc` and command-specific hooks. This can discard protocol messages essential to connection state. There is no default local display.

Despite the name, this is not guaranteed to contain untouched network bytes. The call occurs after `rfc1459_any_to_utf8`, which can rewrite the receive buffer. If conversion allocates a separate larger buffer, the parser uses that separate buffer while this hook still receives the original buffer. Use `raw_irc` when the parser’s converted line is required.

## Example

```text
on -raw_irc_bytes "*" {
    echo Received buffer: $*;
};
```

## Source references

- [source/server.c](../../source/server.c): `server_io` strips line endings, converts input, and gates parsing on this hook.
- [source/parse.c](../../source/parse.c): `rfc1459_any_to_utf8` may rewrite the buffer or allocate another; `parse_server` invokes `raw_irc`.
- [source/hook.c](../../source/hook.c): event registration and standard hook dispatch.
