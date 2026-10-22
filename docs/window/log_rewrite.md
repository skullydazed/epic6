# window log_rewrite

## Synopsis

```text
/window [<window>] log_rewrite ["<expression>"]
```

## Description

Set a rewrite expression for the selected window's [log](log.md), overriding [set log_rewrite](../set/log_rewrite.md). With no selector, use the current window. This neither enables logging nor changes displayed output.

The expression is one argument; quote it if it contains spaces. It is expanded for each logged line after [log_mangle](log_mangle.md) processing. During that expansion, `$0` is the window's user reference number and `$1-` is the processed line text. When assigning the expression inside an alias, use `$$` for each dollar sign that must survive until logging. Additional expansion layers require additional escaping.

No argument removes the override and restores the global rewrite; it does not report the expression. `/window -log_rewrite` does nothing.

## Examples

Define and run an alias that installs the deferred expression, then remove the override:

```text
/alias setup_window_log { /window 3 log_rewrite "[$$0] $$1-"; }
/setup_window_log
/window 3 log_rewrite
```

## Source references

`windowcmd_log_rewrite` in [source/window.c](../../source/window.c), `add_to_window` in [source/screen.c](../../source/screen.c), and `add_to_log` in [source/log.c](../../source/log.c).
