# window show_all

## Synopsis

```text
/window [<window>] show_all
```

## Description

Attempt [window show](show.md) repeatedly on hidden windows. The invisible-window list is global, so candidates are not restricted to the selected window's server or screen. With no hidden windows, the operation does nothing.

Each successful show adds a split window to the global current window's screen, or the most recently used input screen if global current selection is hidden. It makes that window globally current and its screen's input window. Later successful shows normally use the same screen. A selector before `show_all` does not choose the insertion screen.

The usual show rules apply: visible windows are split to make room, controlled by [set always_split_biggest](../set/always_split_biggest.md), and the shown windows keep their contents, settings, channels, queries, and server associations. Showing resets notified flags and activity indications. No IRC request is sent and no hidden window is destroyed.

## Partial completion and limits

The handler starts again at the front of the hidden list after every attempt. It does **not** skip an unshowable window and continue to later entries. If the first candidate has [window swappable](swappable.md) off, it repeatedly reports `Window <name-or-number> can't be made visible`. If there is insufficient screen space, it can repeatedly report `Not enough room for another window!` and put the failed candidate back at the front.

A loop guard stops processing after at most 102 attempts, whether successful or failed, without a separate limit message. Thus some or all windows can remain hidden, and the same diagnostic can appear many times. Earlier successful shows are retained. Use individual `window show` commands to choose other candidates when a front entry blocks progress.

## Arguments and selection

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations on the **original selected window**, even though successful shows can change global current and input selection. `/window -show_all` is a no-op. Successful processing normally produces no confirmation message, though selection hooks can produce output.

## Examples

```text
/window show_all
/window list
```

Attempt to show the hidden windows, then inspect which remain hidden. `show_all` does not guarantee enough space exists to display every window.

## History

This operation first appeared during EPIC4.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_show_all` restarts hidden-list traversal and enforces the attempt guard; `traverse_all_windows_on_screen2` selects the global hidden list; `show_window` handles swappability, insertion, failure, and selection; `add_to_window_list` implements splitting and notification reset.
