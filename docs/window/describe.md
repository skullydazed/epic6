# window describe

## Synopsis

```text
/window
/window [<window>] describe
```

## Description

Display information about the selected window. With no selector, describe the current window. A bare [window](../commands/window.md) command uses this operation by default.

The report includes:

- Window name, reference number, associated server, and screen number. An invisible window has screen number `-1`.
- Geometry and cursor information, including the output height and distances from the normal, held, and scrollback views to the newest output.
- Current channel, all associated channels, pending channel associations when present, current query, and the target name list when present.
- Status-bar count, logging state and filename, notification state and levels, window routing levels, and lastlog levels and sizes.
- Hold mode, hold-slider percentage, held-line status update interval, fixed/skip/swappable settings, `beep_always`, and scrollback adjustment on resize.

The target name list includes entries added through [window add](add.md) as well as query targets. `<None>` indicates that there is no current channel or query. A server association in the report does not independently establish that a live connection exists.

This built-in operation prints local client state rather than querying the IRC server. It does not change the described window's settings, input focus, or associations. The report itself is ordinary output and can be logged or retained according to the receiving window's configuration.

## Geometry details

`Geometry Info` contains nine values in this order: visible topline count, top output row, bottom output row, a literal zero placeholder, output row count, output cursor row, normal-view distance, held-view distance, and scrollback-view distance. These are diagnostic values rather than a separate geometry-setting syntax.

The current `CO, LI are` line prints the terminal geometry helper's values in lines-then-columns order despite its label. Treat it as diagnostic output; it is not necessarily the dimensions of the selected window's own screen.

## Arguments and examples

There are no operation-specific arguments or options. Additional words are processed as subsequent window operations. `/window -describe` also prints the report, since this handler does not suppress reporting for the minus form.

```text
/window
/window describe
/window 3 describe
```

The first two describe the current window; the third describes window 3 without selecting it as the input window.

## Source references

- [source/window.c](../../source/window.c): `windowcmd_describe` emits the report; `windowcmd` invokes it by default when no window operations are supplied.
- [source/term.c](../../source/term.c): `get_term_geometry` returns the line and column values used in the diagnostic geometry line.
