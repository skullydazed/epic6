# $ischanop()

## Synopsis

```text
$ischanop(<nick> <channel>)
```

Returns whether a nickname is a channel operator according to EPIC's channel cache.

## Technical

Both arguments are required; omitting either returns the empty string. There is no default nickname. A literal `*` for `<channel>` selects the current window's channel; omitting the channel does not. Additional arguments are ignored.

The function looks up the nickname in the channel cache for the current server context. Channel and nickname comparisons use IRC case-insensitive matching. It returns the cached operator flag, which is updated from channel membership information and `+o`/`-o` mode changes. Half-operator or voice status alone does not qualify.

This function sends no IRC request. EPIC must track the channel and the nickname; otherwise it returns `0`. Before the channel's initial synchronization finishes, a `0` result can reflect incomplete information. See [chansyncing](chansyncing.md).

When connected to several servers, the current window's channel and the function's server context can differ. Using `*` in that situation can return `0` even when the nickname is an operator elsewhere. Specify the channel explicitly and use [xeval](../commands/xeval.md) with `-s` when checking another server. With no server context, the channel lookup falls back to the primary server.

## Practical

Scripts can use this function to avoid giving channel operator status (`+o`) to someone who already has it. The cached result is a useful condition, but does not establish that you have permission to change their modes.

## Returns

| Result | Meaning |
| --- | --- |
| `1` | The cached nickname has channel operator status. |
| `0` | The cached nickname is not an operator, or the channel or nickname is not tracked. |
| Empty string | A required argument is missing. |

## Examples

```text
$ischanop(alice #example)
$ischanop($N *)
```

The first checks Alice on `#example`; the second checks your nickname on the current window's channel, using the current server context.

## History

This function first appeared in ircII-2.2pre1.

## Source

Registered and implemented as `function_ischanop` in [source/functions.c](../../source/functions.c). Cache lookup, nickname comparison, and operator updates are in [source/names.c](../../source/names.c); required argument handling is in [include/functions.h](../../include/functions.h).
