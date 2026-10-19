# set output_rewrite

## Synopsis

```text
/set output_rewrite [<template>]
/set -output_rewrite
```

Default: unset. With no template, [set](../commands/set.md) displays the current value. Unsetting disables rewriting.

## Description

When defined, expands the template for each logical line reaching normal fullscreen window output and substitutes the expansion for the original text. A logical line is processed before wrapping into screen rows, so a timestamp is added once per logical line rather than once per wrapped row.

| Argument | Meaning |
| --- | --- |
| `$0` | User-visible refnum of the destination window. |
| `$1-` | Original text of the logical line. |

The template is expanded as an expression-containing string, not executed as a command block. An empty expansion supplies an empty output line; it is not a request to discard the record.

## Examples

At the interactive command line, timestamp output using the cached clock:

```text
/set output_rewrite $$Z $$1-
```

Use a custom timestamp format:

```text
/set output_rewrite $$strftime(%X) $$1-
```

Prefix output with a user-defined function's result, passing the destination window:

```text
/set output_rewrite $$myfunc($$0) $$1-
```

`myfunc` must already exist. Preserve dollar signs for expansion when output is rewritten: ordinary command parsing can otherwise expand them while assigning the setting. Additional quoting can be needed inside another alias or expansion layer.

## Ordering and limitations

[On window](../on/window.md) runs before this rewrite. If it suppresses the line, rewriting is skipped. For accepted output, the rewritten text is passed to window and selective logging, lastlog storage, and then display mangling and wrapping. Their own logfile rewrites can apply afterward. The global session log is written earlier, before window routing, so it does not acquire this rewrite.

This supplies a central final edit for window text without requiring separate handlers for each message type. It is not universal: non-fullscreen output bypasses it, as does privileged output such as the internal diagnostic path. It does not rewrite status bars, input prompts, or previously stored output.

If template expansion itself produces output, nested rewrites are allowed only while the rewrite recursion depth is below five; deeper output passes without this rewrite. Keep formatting functions free of unnecessary output or side effects. Expansion runs for each applicable line, so expensive functions can make busy-window output slower.

## Source references

- [source/vars.c](../../source/vars.c): string registration and unset default.
- [source/screen.c](../../source/screen.c): `add_to_window` runs the hook and bounded rewrite before window logs, lastlog, normalization, and wrapping; `add_to_screen` has a separate non-fullscreen path.
- [source/output.c](../../source/output.c): `put_echo` performs global session logging before window routing; privileged diagnostic output bypasses rewriting.
