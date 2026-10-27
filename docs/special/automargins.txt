# Automargins and copying long URLs

EPIC's terminal handling can use the final display column when the terminal description supports it. Together with plain continuation formatting, this can make long URLs easier to select and copy. The original documentation dates the improved support to EPIC5 1.4; the instructions below reflect current EPIC6 source.

## Terminal setup

Use a terminal emulator and a `TERM` description that agree about the terminal's capabilities. Set `TERM` before starting EPIC, then restart EPIC after changing it: terminal initialization reads the environment and loads the terminal database when the client starts. Changing a shell variable or detaching and reattaching a multiplexer does not reload an already-running client's terminal description.

The original document suggested `export TERM=vt102am`. That historical example is not a universal setting: use the description appropriate for your actual terminal, including the terminal exposed inside a multiplexer.

With [`automargin_override`](../set/automargin_override.md) off, EPIC's current terminal code uses the last column when the loaded terminal data contains both `am` (automatic margins) and `rmam` (the sequence for disabling automatic margins). It emits `rmam` when enabling last-column use. Otherwise, it enables automatic margins and reserves a column. With the override on, EPIC attempts last-column use without those checks; the terminal still needs to handle that behavior correctly.

To inspect the relevant control-string capabilities, use:

```irc
/eval echo Disable automargins: $getcap(TERM exit_am_mode)
/eval echo Enable automargins: $getcap(TERM enter_am_mode)
```

[`getcap`](../functions/getcap.md) returns printable forms of the control strings by default. A blank `enter_am_mode` result alone does not establish whether EPIC can use the last column: the current selection checks `am` and `exit_am_mode`, not `enter_am_mode`. Also, `getcap()` currently returns string capabilities only, so querying the boolean `auto_right_margin` through it yields an empty result rather than a boolean answer.

## Output formatting

Remove continuation markers and indentation so that EPIC does not insert that extra display text into wrapped URLs:

```irc
/set -continued_line
/set indent off
```

[`continued_line`](../set/continued_line.md) supplies the continuation prefix; unsetting it removes that prefix. [`indent`](../set/indent.md) is a boolean, so use `off` explicitly. Changing the global indentation setting applies it to every existing window as well as the default for new windows.

If you want a prefix on the first row of each logical output line, you can set one separately:

```irc
/set first_line :::
```

[`first_line`](../set/first_line.md) is optional. Check any [`output_rewrite`](../set/output_rewrite.md) you use so that your display has the prefix you intend.

Restrict configurable word-break characters to a space, keeping punctuation such as commas, dots, and semicolons from becoming additional preferred break points:

```irc
/eval set word_break $chr(32)
```

This assigns one literal space without requiring invisible trailing spaces in a copied command. The equivalent interactive entry is `/set word_break` followed by two spaces and Enter: the first space separates the setting name from its value, and the second is the value. Do not put quotes around the space when using `/set`, because its string value is taken literally.

[`word_break`](../set/word_break.md) controls preferred break opportunities; it does not prevent wrapping an overlong URL. Spaces and explicit newlines also have their own wrapping behavior. EPIC still splits text as needed to fit the display width.

## Reformat existing scrollback

After changing the formatting, rebuild retained output in the current window:

```irc
/window rebuild_scrollback
```

The current operation is [`window rebuild_scrollback`](../window/rebuild_scrollback.md), replacing the original document's obsolete `REBREAK_SCROLLBACK` spelling. It reconstructs the window display from retained lastlog entries using the current formatting. Repeat it for other windows as needed; rebuilding cannot recover entries that are no longer retained in lastlog.

These settings affect EPIC's display layout. Successful copying across wrapped display rows also depends on the terminal's selection and copy behavior; the settings do not guarantee how a terminal joins selected rows.

## Source references

- [source/term.c](../../source/term.c): `term_init()` loads `TERM`; `term_establish_last_column()` chooses last-column handling; `get_term_capability()` implements capability lookup.
- [source/functions.c](../../source/functions.c): `getcap()` argument handling and `chr()` character generation.
- [source/vars.c](../../source/vars.c): string-setting parsing and `set_indent()` update behavior.
- [source/screen.c](../../source/screen.c): `prepare_display()` applies prefixes, indentation, word breaks, and width-based wrapping.
- [source/window.c](../../source/window.c): `REBUILD_SCROLLBACK` registration and reconstruction from lastlog.
