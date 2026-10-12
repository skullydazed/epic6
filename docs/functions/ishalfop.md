# $ishalfop()

## Synopsis

`$ishalfop(<nick> [<channel>])`

## Technical

Returns a nickname's half-operator status from EPIC's channel cache. The nickname is required; omitting it returns the empty string. An omitted channel or literal `*` selects the current window's channel.

Only the nickname is parsed as a function argument. The entire remaining text is the channel name, so additional text is not ignored. Channel and nickname lookup use IRC case-insensitive matching.

The cache lookup uses the current execution context's server (`from_server`). With multiple servers, the current window's channel can belong to another server, making the default channel lookup return `0`. Specify the channel explicitly and use [xeval](../commands/xeval.md) with `-s` to check another server context. With no server context, channel lookup falls back to the primary server.

The function sends no IRC request. EPIC must track both the channel and nickname; otherwise it returns `0`. Before initial channel synchronization finishes, the information may be incomplete. See [chansyncing](chansyncing.md).

Half-operator status is independent of operator and voice status. An observed `%` membership prefix records half-operator status, and `+h`/`-h` mode changes set it to `1`/`0`. When another user has an `@` prefix without a known half-operator indication, EPIC records unknown status (`-1`); this inference does not apply to your own nickname. The result reflects what the server has exposed and EPIC has cached, not a guarantee about permissions on every server.

## Practical

Use this as a condition for actions that depend on known half-operator status. Compare with `1` when you need a known positive result: `-1` is also nonzero and means unknown.

## Returns

| Result | Meaning |
| --- | --- |
| `1` | The cached nickname has half-operator status. |
| `0` | The cached nickname is not a half-operator, or the channel or nickname is not tracked. |
| `-1` | The cached half-operator status is unknown. |
| Empty string | The required nickname is missing. |

## Examples

```text
$ishalfop(alice #example)
$ishalfop($N *)
```

The first checks Alice on `#example`; the second checks your nickname on the current window's channel using the current server context.

## Source

Registered and implemented as `function_ishalfop` in [source/functions.c](../../source/functions.c). Channel lookup, `is_halfop`, membership-prefix handling, and mode updates are in [source/names.c](../../source/names.c).
