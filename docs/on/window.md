# on window

## Synopsis

```text
/on [<noise>]window "<pattern>" [(<argument list>)] { <commands> }
```

## Description

Triggered for ordinary output directed to a window, before the line is processed for display there. It applies to hidden as well as visible windows; it is not a notification that a line has actually appeared on the terminal.

Use standard [on](../commands/on.md) matching and noise controls. The pattern matches the destination window refnum and text together. There are no event-specific options.

## Parameters

| Parameter | Meaning |
| --- | --- |
| `$0` | Destination window's user-visible refnum, not its name. |
| `$1-` | Output text before window-level rewriting, display normalization, and wrapping. |

A long line that later wraps into several screen rows produces one event at this stage. Text can contain formatting controls and message prefixes already added by the producer.

## Suppression and output processing

In full-screen mode, a suppressing hook, such as a serial-zero `^window` hook, stops the line before output rewriting, window and selective logging, lastlog insertion, scrollback insertion, terminal display, and hidden-window notification. It does not undo the event that produced the text. The global session log is written earlier in the normal output path and is not undone by this hook.

If the hook permits handling, [output_rewrite](../set/output_rewrite.md) runs next, followed by logging and display processing. Changing hook arguments does not replace the original line; suppress it and explicitly output replacement text when needed.

The hook is nonrecursive. Ordinary `echo` or `xecho` output produced within a running handler bypasses another invocation of this same hook, so a replacement line can be printed without recursively re-entering it. Explicitly select the destination window when replacing text: the output destination in `$0` need not be the current input window.

In dumb mode, the output path instead uses the current window, records lastlog before invoking this hook, and prints to standard output only if the hook permits it. Suppression there does not remove the already recorded lastlog entry. The full-screen window rewriting and scrollback path is not used.

Privileged internal diagnostics bypass this hook. Direct redraws and rebuilding existing scrollback are also not new window-output events. Status bars use the separate [on status_update](status_update.md) event.

## Example

Replace output routed to window 2 while retaining its destination:

```text
on ^window "2 *" (win, text) {
    xecho -w $win [window $win] $text;
};
```

The nested `xecho` bypasses this hook while the handler is active. Use this destination-preserving example in full-screen mode; dumb mode uses the current-window path described above.

## Source references

- [source/screen.c](../../source/screen.c): `add_to_screen` routes output and handles dumb mode; `add_to_window` invokes the hook before rewriting, logging, scrollback, and display.
- [source/output.c](../../source/output.c): `put_echo` writes the global session log before routing output to the screen.
- [source/hook.c](../../source/hook.c): registers `window` as nonrecursive and implements matching and suppression.
- [source/commands.c](../../source/commands.c): `xechocmd` implements explicit window selection with `-w`.
