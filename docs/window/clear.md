# window clear

## Synopsis

```text
/window [<window>] clear
```

## Description

Move the selected window's normal scrolling view to the end of its scrollback, leaving that view empty until new output arrives. With no window selector, operate on the current window. The next line of normal output appears at the top of the cleared output area.

The scrollback lines are retained and can still be viewed by scrolling backward. Clearing records a boundary at the current end of scrollback, resets the window's notification flag and activity indicator, and requests window-body and status-bar redraws. It leaves input text, lastlog, and saved log files unchanged and sends no IRC request.

This built-in operation uses the same clearing helper as [clear](../commands/clear.md) with no selection options, but applies it to the window selected by the [window](../commands/window.md) command. Use `/clear -all`, `-visible`, or `-hidden` to select multiple windows.

A held view or a view scrolled backward has its own display position and can continue showing earlier lines after clearing. This operation does not explicitly unhold the window or reset those positions.

## Restoring output

[unclear](../commands/unclear.md) without `-noforce` removes the clear boundary and brings recent retained lines back into the current window's normal view. In contrast, [window unclear](unclear.md) respects the saved boundary, so immediately after clearing it can leave the normal view empty rather than restore the older lines. Scrolling backward can still reveal those retained lines.

## Arguments and limitations

There are no arguments or options specific to this operation. Additional words are processed as subsequent window operations. `/window -clear` is a no-op. The clearing helper does nothing outside fullscreen mode. Clearing an invisible window changes its normal view for later display without making it visible.

## Examples

```text
/window clear
/window 3 clear
```

Clear the current window, or window 3, respectively. To bring recent output back into the current window while removing the clear boundary:

```text
/unclear
```

## Source references

- [source/window.c](../../source/window.c): `windowcmd_clear`, `clear_window`, `windowcmd_unclear`, and `unclear_window` implement clearing and boundary-aware restoration.
- [source/commands.c](../../source/commands.c): `e_clear` implements multi-window selection and forced `/unclear` restoration.
- [source/screen.c](../../source/screen.c): window redraw chooses normal, held, or scrollback display positions.
