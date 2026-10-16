# window refresh

## Synopsis

```text
/window [<window>] refresh
```

## Description

Process pending window updates immediately within a `/window` command, rather than waiting for the command to finish. EPIC normally batches updates across several window operations to reduce distracting flicker, particularly over slow links.

This operation marks every window's status for updating and calls the general window-update routine. Its scope is client-wide, not limited to the selected window or its screen. Pending scrollback rebuilds, output-height adjustments, and necessary visible-window redraws can be handled at this point. Invisible windows receive applicable logical updates but are not made visible.

This is not an unconditional repaint of every window body: output areas are redrawn when their existing update state requires it. Status updates also remain subject to the status-update gate. The `/window` dispatcher normally disables status generation and drawing until the end of the command, so `refresh` does not guarantee an immediate status-bar redraw in the middle of a compound command.

Use [window rebuild_scrollback](rebuild_scrollback.md) when you specifically need reconstruction from lastlog; `refresh` does not itself request a rebuild. It does not clear stored output, change selection or window geometry on its own, or send an IRC request. Pending operations that it processes can have their normal effects and hooks.

## Arguments and output

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations on the same selected window. `/window -refresh` is a no-op. A selector before `refresh` does not restrict the update scope. The operation produces no ordinary confirmation message.

## Examples

```text
/window refresh
/window grow 2 refresh shrink 2
```

Process outstanding updates, or place an update point between a grow and shrink operation. The second example requires another visible window that can participate in resizing; status drawing can still remain deferred until the command ends.

## History

This operation first appeared during EPIC4.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_refresh`, `update_all_status`, and `update_all_windows` implement global update requests and processing. `windowcmd` disables status updates during compound operations and restores them afterward.
- [source/status.c](../../source/status.c): `make_status`, `redraw_status`, and `permit_status_update` enforce and release deferred status updates.
