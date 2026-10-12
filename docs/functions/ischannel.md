# ischannel function

## Synopsis

`$ischannel(<channel name>)`

## Technical

Tests whether the first character of the supplied name is a recognized channel prefix. It uses the current server context's cached `005 CHANTYPES` value when nonempty; otherwise it falls back to `+`, `#`, `&`, and `!`. There is no separate server-selector argument.

Only the prefix is tested. This does not validate the rest of the name, require a character after the prefix, check whether the channel exists, or check whether you have joined it. It sends no server query. A name such as `#` can return one even though the server may reject it as a join target.

The name is the first function argument word; additional words are ignored. Quote-aware parsing follows `/xdebug dword`. Missing input returns empty. An explicitly empty name, when parsed as one argument, returns zero.

## Practical

Scripts can use this to add a default `#` prefix when the user enters a bare channel name:

```irc
alias join (channel, ...) {
    if (ischannel($channel)) {
        //join $channel $*
    } else {
        //join #$channel $*
    }
}
```

The doubled slash invokes the built-in [join](../commands/join.md) rather than this alias. This is a convenience for a server supporting `#` channels, not complete channel-name validation.

## Returns

`1` for a recognized leading prefix, otherwise `0`. Missing the required argument returns empty. No diagnostic is printed.

## Examples

With the fallback prefix set, `$ischannel(#blah)` and `$ischannel(&blah)` return `1`, while `$ischannel(blah)` returns `0`. A server advertising a different nonempty `CHANTYPES` set can change these results.

## History

First appeared in ircII-2.2pre1.

## Source

Registration and first-word parsing: [source/functions.c](../../source/functions.c). `is_channel()` prefix test and fallback: [source/parse.c](../../source/parse.c). Cached `005` settings: [source/server.c](../../source/server.c).
