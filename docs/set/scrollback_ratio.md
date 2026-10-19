# set scrollback_ratio

## Synopsis

```text
/set scrollback_ratio [<percent>]
```

Default: `50`. With no value, [set](../commands/set.md) displays the current value. Negative and nonnumeric values are rejected.

## Description

Determines what percentage of a window's display height to move when scrolling backward or forward by the default amount. It applies to the [scroll_backward](../bind/scroll_backward.md) and [scroll_forward](../bind/scroll_forward.md) bindings and to [window scroll_backward](../window/scroll_backward.md) and [window scroll_forward](../window/scroll_forward.md) when they request the default distance.

For each action, EPIC clamps the effective percentage to `1` through `100`, multiplies it by that window's display height, and divides by 100 with integer truncation. The resulting distance is at least one display row. For example, `50` moves ten rows in a twenty-row window, or ten rows in a twenty-one-row window.

Zero and values above 100 can remain stored: the clamp is applied when scrolling, without changing the setting. Zero therefore behaves like one percent, subject to the one-row minimum. This does not disable scrolling.

## Scope

This is a global percentage read for each action, so differently sized windows move different distances. A window command with a nonzero explicit line count uses that count rather than this ratio. Movement stops at the available history boundaries; the calculated distance does not guarantee that many rows remain to traverse.

The setting does not control automatic scrolling when new output fills a window; see [scroll_lines](scroll_lines.md). It also does not change scrollback retention, hold-mode advancement, or start/end navigation. Changing it affects future actions and does not immediately move any window.

## Example

```text
/set scrollback_ratio 75
/window scroll_backward
```

In a twenty-row window, the default backward action requests fifteen rows. Use `/set scrollback_ratio 50` to restore half-window movement.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): integer registration and default 50.
- [source/window.c](../../source/window.c): `window_scrollback_backward` and `window_scrollback_forward` clamp the percentage and calculate rows; window commands distinguish default and explicit counts; line movement stops at history boundaries.
- [source/keys.c](../../source/keys.c): registers the forward and backward scroll bindings.
