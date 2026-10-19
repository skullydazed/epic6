# set status_scrollback

## Synopsis

```text
/set status_scrollback [<text>]
/set -status_scrollback
```

Default: ` (Scroll)`, including a leading space. With no text, [set](../commands/set.md) displays the current value. Unsetting removes the marker rather than leaving scrollback mode.

## Description

Sets the status-bar string displayed while a window is using its scrollback view. It supplies the `%K` field in [status_format](status_format.md), or a per-window format. The field is empty when that window is not in scrollback mode.

The marker is inserted directly: `%K` inside `status_scrollback` has no nested substitution meaning. The outer format must contain `%K`. Each window uses its own scrollback state without the current-window restriction of [show_status_all](show_status_all.md).

A visible marker can remind you to return to live output. Normal [scrollback](scrollback.md) trimming is deferred while a scrollback view is active, so continued output can grow the buffer beyond its configured target until trimming resumes.

## Related field and effects

The outer `%{1}K` variant shows fixed position information such as `(Scroll: 20 of 100)` instead. It does not use this setting, so unsetting `status_scrollback` does not hide that variant. [Status_hold](status_hold.md) and [status_hold_lines](status_hold_lines.md) can separately indicate output beyond the current view.

Changing the marker rebuilds status formats and requests updates. It does not move the view, release held output, discard history, or change retention limits. Ordinary command parsing can expand dollar expressions on assignment; [status_does_expandos](status_does_expandos.md) can additionally expand dollar expressions in the assembled status line.

## Example

Customize the scrollback indicator:

```text
/set status_scrollback [Scrolling]
```

It appears wherever `%K` is present while that window is browsing scrollback. `/set -status_scrollback` hides the text without returning the window to live output.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, status rebuild callback, and default marker.
- [source/status.c](../../source/status.c): `%K` uses `status_scrollback`; `%{1}K` uses the independent `status_scroll_info` formatter.
- [source/window.c](../../source/window.c): scrollback view state and deferred trimming in `trim_scrollback`.
