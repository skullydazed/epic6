# $lastlog()

## Synopsis

`$lastlog(<window> <pattern> <level> ...)`

## Technical

Searches a window's lastlog for entries whose text matches the wildcard pattern and whose level is included in the supplied level mask. This function returns line positions; it does not display matching text like the [lastlog command](../commands/lastlog.md).

The window and pattern are required. The window can be a window reference or name; `0` selects the current window. Invalid windows return the empty string. The pattern is always a double-quoted-word argument, regardless of `xdebug dword`, so quote patterns containing spaces. Matching uses EPIC's case-insensitive wildcard matcher against the whole stored message; use surrounding `*` to search for a substring. Regex and command-style search options are not supported.

The remaining input selects levels, separated by spaces or commas. `all` includes every level; `none` or `-all` clears the mask, and `-<level>` removes a level. Names are matched case-insensitively by prefix. Unsupported levels produce a warning and are ignored; accepted levels still apply. Omitting levels leaves an empty mask and therefore returns no matches.

Only lastlog entries marked visible in the selected window are searched. Here visibility is an entry flag, not whether the window is currently on screen. Results are one-based positions among that window's visible lastlog entries: `1` is the newest, and every visible entry advances the position even if it fails the pattern or level test.

## Practical

Use the returned positions with [line](line.md) to obtain matching text, supplying the same window. They remain meaningful only while that window's lastlog contents and visibility have not changed. They are not persistent entry reference numbers; see [lastlogctl](lastlogctl.md) for reference-based access.

## Returns

A space-separated list of matching line positions in increasing numeric order, from newest toward oldest. Missing required arguments, an invalid window, an empty level mask, or no matches returns the empty string. Searching does not alter the lastlog.

## Examples

```text
$lastlog(0 "*connection lost*" all)
$lastlog(0 * public msgs)
```

The first returns positions of entries containing `connection lost` in the current window. The second selects entries at either `public` or `msgs` level. If only the newest and third-newest visible entries match, the result is `1 3`.

## Source

Registered in [source/functions.c](../../source/functions.c) and implemented as `function_lastlog` in [source/lastlog.c](../../source/lastlog.c). Window selection is in [source/window.c](../../source/window.c); level-mask parsing and warnings are in [source/levels.c](../../source/levels.c).
