# on raw_irc

## Synopsis

```text
/on [<noise>]raw_irc "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Offered each nonempty incoming IRC line when it reaches the server-message parser, before command dispatch, CTCP handling, and ordinary named events. This allows scripts to intercept server extensions or messages that need different processing.

Prefer a more specific event when one provides the required information: this hook participates in processing every incoming line. A suppressing handler bypasses normal parsing and can prevent essential state updates or protocol responses, including replies needed to keep a connection alive.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the text described below. There are no event-specific options.

## Arguments and framing

The hook receives one line with line endings removed and incoming character-set conversion already applied:

- If the line begins with `:`, EPIC removes that first colon before supplying `$*`.
- Otherwise EPIC prepends `* ` to the line supplied to the hook.

For a conventional untagged prefixed line, the usual fields are:

| Parameter | Meaning |
| --- | --- |
| `$0` | Complete sender prefix, which can include `!user@host`; not necessarily a server name. |
| `$1` | Protocol command or numeric token. |
| `$2-` | Remaining protocol text, including trailing-parameter punctuation. |

For an unprefixed line, `$0` is the synthetic `*`, followed by the command and arguments. These positions are not reliable for IRCv3-tagged lines: a line beginning `@tags` also receives the synthetic `*`, with the tags and any subsequent prefix still present. This hook runs before the parser extracts tags and splits the sender prefix. Parse the actual line structure when tags are possible.

## Ordering and suppression

[On raw_irc_bytes](raw_irc_bytes.md) runs earlier in the server-read path and can prevent this event from being reached. Although its name is raw, `raw_irc` receives the converted text rather than necessarily the original wire bytes. It runs before the configured inbound line mangling and normal tokenization.

There is no direct built-in output for this event. If it permits default handling, EPIC continues parsing the original line. A suppressing hook, such as a serial-zero `^raw_irc` hook, returns from the parser without normal handling of that line. Merely registering a nonsuppressing handler does not stop parsing.

This suppression can prevent numeric state processing, channel-cache updates, CTCP handling, and later message hooks. It does not undo work already done in the server-read path or stop the server from sending more data. The hook does not itself send an IRC request or interpret a modified `$*` as a replacement server line.

## Example

Observe incoming lines without suppressing normal parsing:

```text
on -raw_irc "*" {
    echo Incoming IRC: $*;
};
```

The quiet `-` mode preserves default handling. This is a diagnostic display of the hook's converted and prefix-adjusted text, not a byte-for-byte wire capture.

## Source references

- [source/server.c](../../source/server.c): the server-read path strips line endings, converts input, offers `raw_irc_bytes`, and invokes `parse_server`.
- [source/parse.c](../../source/parse.c): `parse_server` adjusts the initial prefix for this hook and honors suppression before mangling, tag extraction, tokenization, and dispatch.
- [source/hook.c](../../source/hook.c): event registration and `do_hook` implement matching and suppression.
