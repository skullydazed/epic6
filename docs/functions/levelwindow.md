# $levelwindow()

## Synopsis

`$levelwindow(<server refnum> <level descriptions>)`

## Technical

Returns the user-visible window reference number of the first window assigned to the specified server whose window level mask includes any requested level. Visible and hidden windows are considered. This tests the window's output-routing levels, not its lastlog retention mask.

The server reference is required and is compared directly with each window's assigned server. There is no current-server default or special substitution for `-1`; `-2` can match windows assigned to no server. Nonnumeric text converts to zero. Integer conversion uses base detection, including hexadecimal and leading-zero octal.

The remaining input uses the same level-mask syntax as [window](../commands/window.md) `level`: space- or comma-separated case-insensitive level names, prefixes, `all`, `none`, `-all`, and `-<level>` exclusions. Unsupported levels are silently ignored. Missing level descriptions produce an empty mask and return `-1`.

If several windows match, the first in EPIC's window traversal order wins. Use a single level to avoid ambiguity when different windows claim different requested levels. Windows with level `none` do not match. The function sends no request and does not change windows or their masks.

## Practical

Use this to locate a window claiming an output level for a given server. It can help when choosing a destination for [xecho](../commands/xecho.md), but does not reproduce every output-routing rule: explicit windows, channel/query targets, and routing fallbacks can select another destination.

## Returns

The matching window's user-visible reference number, or `-1` when none matches. Missing the required server argument returns the empty string. A valid server with no qualifying window also returns `-1`.

## Examples

```text
$levelwindow(1 notice)
$levelwindow(0 msgs,public)
```

The first finds a window on server reference `1` claiming `NOTICES`. The second returns the first window on server reference `0` claiming either `MSGS` or `PUBLICS`; it does not mean the current server.

## History

This function first appeared in EPIC4-1.1.12.

## Source

Registered and implemented as `function_levelwindow` in [source/functions.c](../../source/functions.c). Window masks, server assignments, user reference numbers, and traversal are in [source/window.c](../../source/window.c); level parsing is in [source/levels.c](../../source/levels.c).
