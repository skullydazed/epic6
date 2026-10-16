# window swap

## Synopsis

```text
/window [<window>] swap <hidden-window|last>
```

## Description

Replace the selected visible window with a hidden window, making the original hidden. With no window selector, replace the current window. The destination can be a hidden refnum, [window name](name.md), or UUID. Names and UUIDs are case-insensitive, and lookup is global across servers.

`last` selects the first entry in the invisible-window list, normally the window most recently hidden by hiding, hidden creation, or a swap. Case-insensitive abbreviations such as `l` are accepted and take precedence over a destination name. This selection does not search past an unswappable first entry. Use [window next](next.md) or [window previous](previous.md) to cycle eligible hidden windows by refnum.

The replacement takes the source's position and total space. Its output height is adjusted for its own status bars and toplines; if the overhead exceeds that space, the output height is set to zero and screen geometry is recalculated. Each window retains its own contents, server, channels, queries, and settings. Neither window is destroyed.

If the source was its screen's input window, the replacement takes that role. If the source was EPIC's global current window, the replacement becomes current. An explicit selector can swap a different visible window without changing global current selection. The replacement's notified flag and activity indication are cleared. No IRC request is sent.

## Restrictions and output

The source must be visible, the destination hidden, and both must have [window swappable](swappable.md) on. `skip` and `fixed` do not by themselves prevent an explicit swap.

- A missing argument reports `SWAP: Please specify a window refnum or LAST`.
- `last` with no hidden windows reports `SWAP: There are no invisible windows`.
- An unknown destination reports `SWAP: No such window: <destination>`.
- A visible destination reports `SWAP: Window <name-or-number> is not invisible!`.
- A hidden source reports `You can only SWAP an invisible window with a visible window.`.
- An unswappable participant reports `Window <name-or-number> is not swappable`.

These checks leave visibility unchanged. Successful swapping normally produces no confirmation message, though selection hooks can produce output.

## Arguments and subsequent operations

The destination is one whitespace-delimited word; use a refnum or UUID for a name containing spaces. There are no other options. Additional words are processed as subsequent window operations using EPIC's resulting **global current window**, even if an explicit selector chose another swap source or a swap failed. `/window -swap` is a no-op.

Unlike [window show](show.md), swapping does not add another split window. Use [window refnum_or_swap](refnum_or_swap.md) when a destination may already be visible.

## Examples

```text
/window swap 3
/window swap last
/window 7 swap chat
```

Replace the current window with hidden window 3, replace it with the first hidden window, or replace visible window 7 with hidden window `chat`.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_swap`, `get_invisible_window`, and `get_window_from_args` implement parsing, `last`, diagnostics, and subsequent targeting. `swap_window` implements checks, geometry, visibility, selection, and notification resets; `add_to_invisible_list` maintains the hidden-list front.
