# chanmode function

## Synopsis

```text
$chanmode([channel ...])
```

## Technical

`chanmode()` returns cached channel-mode strings corresponding, in order, to a space-separated list of channel names. With no arguments, it uses the current window's current channel name. An explicit `*` selects that same name. In an [`on`](../commands/on.md) handler, this default need not be the channel that triggered the event; pass the event's channel explicitly when needed.

Lookups use the current execution server (`from_server`), with primary-server fallback when that context has no server. The default channel name still comes from the current window. There is no server argument or mode filter. Channel names use the server's case mapping, and argument word parsing honors [`xdebug dword`](../commands/xdebug.md).

Each cached string contains sorted mode letters without a leading `+`, followed by the stored key and nonzero user limit, when present, in that order. It describes the tracked channel flags, not ban lists or members' operator/voice modes. Parameter-taking modes other than the specially tracked `k` and `l` are generally omitted.

A `*` result means that no nonempty cached mode string is available for that channel, including an untracked channel or an empty/unsynchronized mode cache. It does not mean merely that the channel lacks a key.

## Practical

This function reads local state and sends no IRC request. You normally must be on the channel for EPIC to have a tracked record. Before channel synchronization finishes, the cache may be empty or incomplete; [`on channel_sync`](../commands/on.md) is the appropriate completion event for initial synchronization. Later results reflect mode information EPIC has processed and can be stale if information is missing. Server policy can conceal a key or supply a placeholder.

Mode strings can contain spaces. With `xdebug dword` enabled, each result containing spaces is double-quoted so it remains one word in the returned list. With that flag disabled, these strings are appended without grouping, so multiple-channel results can be ambiguous. Request one channel at a time when consuming unquoted results. See [`chankey()`](chankey.md) and [`chanlimit()`](chanlimit.md) for individual parameters, or [`mode`](../commands/mode.md) to request server information asynchronously.

## Returns

One result per channel, using `*` for unavailable or empty mode strings. A no-argument call returns one result for the default channel, or `*` if no matching record is available. An explicitly empty first channel argument also selects the default; an empty argument after a result has already been added stops list processing.

## Examples

```text
$chanmode(#example)
$chanmode(#example #other)
$chanmode(*)
$chanmode()
```

If the cached letters for `#example` are `klnt`, with key `secret` and limit 25, its result is `klnt secret 25` with `dword` disabled, or `"klnt secret 25"` with `dword` enabled. An untracked `#other` adds a `*` result.

## History

This function first appeared in EPIC3pre7.

## Source

[source/functions.c](../../source/functions.c) registers `chanmode` and implements `function_channelmode()`. [source/names.c](../../source/names.c) implements channel lookup, mode tracking, and serialization. [source/ircaux.c](../../source/ircaux.c) implements sorted mode insertion and optional result-word quoting. [include/words.h](../../include/words.h) defines argument word parsing.
