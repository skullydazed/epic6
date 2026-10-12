# $key()

## Synopsis

`$key([<channel list>])`

## Technical

Returns cached channel mode `+k` keys for a space-separated list of channels, in the order supplied. With no argument, it checks the current window's current channel. A literal `*` also selects that channel name. These defaults may differ from the channel associated with an [on](../commands/on.md) hook.

Every lookup uses the current execution context's server (`from_server`), with no server selector. With no server context, channel lookup falls back to the primary server. Channel names are matched using the server's IRC casemap. With several servers, the current window's channel name can belong to a different server; specify channel names and use [xeval](../commands/xeval.md) with `-s` when checking another server context.

Input channel words and returned key words honor [xdebug](../commands/xdebug.md) `dword`. With it enabled, a returned key containing spaces is quoted; with it disabled, that key can occupy multiple words. An empty parsed channel in the first position uses the current channel, while an empty channel encountered after a result has been added ends processing.

Each missing or empty cached key is represented by `*`. This includes unknown channels, absent current channels, and unavailable key information, not just channels without `+k`. A literal cached key of `*` is indistinguishable from this placeholder.

## Practical

This is a local cache query and sends no IRC request. EPIC must track the channels. Before their initial synchronization finishes, the function can return `*` even for a keyed channel; see [chansyncing](chansyncing.md). Server policy can conceal keys or provide placeholders, and the function cannot recover information the server did not send.

Use [chankey](chankey.md) to select an explicit server and retrieve one raw cached key; that function uses an empty result rather than `*` for unavailable information.

## Returns

A list of cached keys or `*` placeholders. Omitted input still produces one result for the default channel, including `*` when no key is available. There is no separate error sentinel.

## Examples

```text
$key()
$key(#one #two)
$key(*)
```

If `#one` has cached key `secret` and no key is available for `#two`, the second expression returns `secret *`.

## History

This function first appeared in “+4” (post-ircII, pre-EPIC). The default argument and literal `*` support first appeared in “+5”.

## Source

Registered and implemented as `function_key` in [source/functions.c](../../source/functions.c). Channel selection and `get_channel_key` are in [source/names.c](../../source/names.c), function-word parsing is defined in [include/words.h](../../include/words.h), and return-word quoting is in [source/ircaux.c](../../source/ircaux.c).
