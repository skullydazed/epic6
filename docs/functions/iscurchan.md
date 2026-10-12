# $iscurchan()

## Synopsis

```text
$iscurchan(<channel name>)
```

## Technical

Returns `1` if the supplied name is the current channel of any window whose server matches the current execution context's server (`from_server`). It checks visible and hidden windows, not just the current window. A channel merely being joined or assigned to a window does not qualify unless it is that window's current channel.

The channel name is required; omitting it returns the empty string. Only the first function argument is used; additional arguments are ignored. A literal `*` is not expanded to the current channel. There is no window or server selector; use [xeval](../commands/xeval.md) with `-s` to check another server context.

Matching uses EPIC's general case-insensitive string comparison, rather than the server's IRC casemap. In particular, do not rely on IRC-specific equivalences such as `[` and `{`. The function reads local window/channel state and sends no IRC request.

## Practical

This function allows more flexibility when testing a channel in an [on](../commands/on.md) hook. Comparing the hook's channel with `$C` checks only the current window's channel; `iscurchan` checks whether it is current in any window on the context's server.

## Returns

| Result | Meaning |
| --- | --- |
| `1` | The name matches a window's current channel on the context's server. |
| `0` | No qualifying window has that current channel. |
| Empty string | The required channel argument is missing. |

## Examples

```text
/on ^send_public * {if (iscurchan($0)) {echo Sent to a window's current channel: $0}}
```

Here `$0` is the outgoing channel from the `send_public` hook. The hook suppresses the usual local display and prints the message only when the channel qualifies.

## Source

Registered and implemented as `function_iscurchan` in [source/functions.c](../../source/functions.c). Window traversal and current-channel access are in [source/window.c](../../source/window.c), channel selection is in [source/names.c](../../source/names.c), and string comparison is in [source/ircaux.c](../../source/ircaux.c).
