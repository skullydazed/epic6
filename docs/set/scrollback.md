# set scrollback

## Synopsis

```text
/set scrollback [<lines>]
```

Default: `256`. With no value, [set](../commands/set.md) displays the current setting. Values must be nonnegative integers; negative and nonnumeric values are rejected.

## Description

Sets the target maximum size of each window's scrollback buffer, measured in display rows after wrapping. Long logical messages can occupy several rows. This controls retained display history, not a file log or the number of entries counted by [lastlog](lastlog.md).

Changing the setting updates every existing window's scrollback maximum, including windows previously configured with [window scrollback](../window/scrollback.md). It also supplies the initial maximum for future windows. Each window's effective maximum is at least twice its display height, so different windows can receive different maxima from the same assignment. The stored global value remains the requested number.

Zero therefore does not disable scrollback: existing windows retain their height-based minimum. Window resizing can raise a window's maximum to maintain that minimum; subsequently shrinking the window does not automatically lower its maximum again.

## Trimming and limits

Changing the maximum does not immediately delete old rows. The trimming routine removes the oldest display rows when the buffer exceeds its maximum and trimming is allowed. New output normally invokes that routine.

Trimming is deferred while the window is in scrollback mode or while held output extends beyond its visible height. The buffer can therefore grow beyond the configured maximum during those conditions. This is a retention target, not a strict memory limit. Deferred excess can be discarded when normal trimming resumes.

Deleting display rows does not itself rewrite logfiles or set lastlog retention. Conversely, increasing this setting does not recover rows already discarded. To explicitly clear display history, see [window flush_scrollback](../window/flush_scrollback.md). Manual navigation distances are controlled separately, including by [scrollback_ratio](scrollback_ratio.md).

## Example

```text
/set scrollback 2000
```

This updates all current windows to a target of 2000 rows, or twice their individual display height if greater, and changes the initial value for new windows. To adjust just one existing window, use `/window scrollback 2000` instead.

## Source references

- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): integer registration and default 256.
- [source/window.c](../../source/window.c): `set_scrollback_size` updates all windows; new-window initialization copies the default; resize handling enforces the height minimum; `trim_scrollback` deletes oldest rows while respecting scrollback and hold conditions.
- [source/screen.c](../../source/screen.c): output is wrapped into display rows, added to scrollback, and followed by trimming.
