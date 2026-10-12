# $ischanvoice()

## Synopsis

```text
$ischanvoice(<nick> [<channel>])
```

Searches EPIC's channel cache for a nickname's voice status on the current server context.

## Technical

The nickname is required; omitting it returns the empty string. The remaining argument text is the channel name. An omitted channel or literal `*` selects the current window's channel. Additional text is part of the channel name, rather than ignored. Channel and nickname lookup use IRC case-insensitive matching.

The function returns the cached voice flag directly; it sends no IRC request. If the channel or nickname is not tracked, it returns `0`. Incomplete synchronization can also make the result incomplete. See [chansyncing](chansyncing.md).

With several servers, the current window's channel can belong to a different server than the function's server context. Specify the channel and use [xeval](../commands/xeval.md) with `-s` when checking another server. With no server context, channel lookup falls back to the primary server.

## Returns

| Result | Meaning |
| --- | --- |
| `1` | The cached nickname has voice (`+v`). |
| `0` | The cached nickname does not have voice, or the channel or nickname is not tracked. |
| `-1` | The cached voice status is unknown. |
| Empty string | The nickname argument is missing. |

## Note

Channel membership replies can show an operator or half-operator prefix while hiding whether that user also has voice. When EPIC sees another user with an `@` or `%` prefix and no explicit voice indication, it records unknown voice status (`-1`). An explicit `+` prefix records `1`, and observed `+v` or `-v` changes set the flag to `1` or `0`.

Operator status does not itself imply voice. The unknown-status inference does not apply to your own nickname, so `0` is not a guarantee that the server has explicitly confirmed the absence of voice. Scripts should compare with `1` when they specifically need known voice status; `-1` is also a nonzero result.

## Examples

```text
$ischanvoice(alice #example)
$ischanvoice($N)
```

The first checks Alice on `#example`; the second checks your nickname on the current window's channel, using the current server context.

## Source

Registered and implemented as `function_ischanvoice` in [source/functions.c](../../source/functions.c). Channel lookup, cached voice flags, prefix handling, and mode updates are in [source/names.c](../../source/names.c).
