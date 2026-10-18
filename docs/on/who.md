# on who

## Synopsis

```text
/on [<noise>]who "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for standard server WHO reply rows that reach EPIC's normal WHO display path. Each eligible row can trigger the hook; it is not a single notification for the entire query. The [who](../commands/who.md) command requests information from the server, whose visibility rules and supported extensions determine the results.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the complete reordered row below. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Channel reported by the server, commonly `*` when no channel is disclosed. |
| `$1` | Nickname. |
| `$2` | Status flags supplied by the server. |
| `$3` | Username portion of the user's address. |
| `$4` | Hostname portion of the user's address. |
| `$5` | User's server. |
| `$6` | Server hop count, when supplied in the standard position. |
| `$7-` | User's realname (IRCNAME). |

Common status flags are `G` for away, `H` for here, `*` for an IRC operator, `@` for a channel operator, and `+` for voice. Servers can supply additional flags; do not assume these are the only possible characters. The hop count and realname come from the server's trailing reply field rather than separate client validation of their contents.

## Filtering and default behavior

WHO command filters are applied before this hook. A query using `who -line { ... }` executes that block instead of this hook and normal display. Internal WHO requests with a C callback also bypass it. Thus receiving a WHO reply does not guarantee an `on who` event. Queued requests sharing a reply can process that reply separately, so one received row can also lead to more than one event.

For a normal row, if `who` permits default handling, EPIC next invokes the numbered `352` hook with the same reordered row. If both permit handling, the default line displays the channel, nickname, status, `user@host`, and the trailing hop-count/realname field. [Channel_name_width](../set/channel_name_width.md) controls channel column padding and truncation; zero uses a tab separator. The server field is present in hook arguments but omitted from that built-in line.

A suppressing hook, such as a serial-zero `^who` hook, prevents the later numbered hook and default line for that row. It does not cancel the query, stop subsequent replies, or alter server information. The event uses the `other` output level and the queued query target as output context.

The handler also has a header-row branch for a status field beginning with `S`: it substitutes `Channel` as `$0` and can invoke this hook before displaying the header, without the later numbered hook. Do not treat such a row as a user record.

Extended WHO replies using numeric `354` follow a separate callback or numbered-hook path and do not invoke `on who`. End-of-WHO handling is separate as well. Standard `352` processing is consumed by the WHO subsystem before the general [on numeric](numeric.md) display hook.

## Example

Replace normal WHO row output:

```text
on ^who "*" (chan, nick, flags, user, host, server, hops, realname) {
    echo WHO $nick on $chan: $user@$host ($flags) $realname;
};
```

Use this for ordinary display queries, not queries whose `-line` block already consumes their rows.

## Source references

- [source/who.c](../../source/who.c): `whoreply` validates, filters, reorders, hooks, and displays standard rows; `xwhoreply` handles extended replies; `whobase` parses query options.
- [source/numbers.c](../../source/numbers.c): routes `352` and `354` into WHO handling before general numeric display.
- [source/hook.c](../../source/hook.c): event registration and standard hook matching and suppression.
