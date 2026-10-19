# set log_rewrite

## Synopsis

```text
/set log_rewrite [<template>]
/set -log_rewrite
```

Default: unset. With no template, [set](../commands/set.md) displays the current value. `/set -log_rewrite` removes the global default rewrite.

## Description

Provides a default template for altering text as it is written to logfiles. When a line reaches the logging routine and no log-specific rewrite was supplied, EPIC expands this template and writes the expansion instead of that line. It does not replace the live window text or modify the lastlog record.

The default can apply to the global [log](log.md), per-window logs, and selective [log](../commands/log.md) objects. A non-null per-window or selective-log rewrite takes precedence for that log; the global template is a fallback rather than an additional second rewrite. The same logical output can be rewritten separately for several log destinations.

## Template arguments

| Argument | Meaning |
| --- | --- |
| `$0` | User-visible destination window refnum when the logging caller supplies a window. |
| `$1-` | Text being logged, after the selected log-mangling pass. |

The global session log runs before window routing and supplies no destination window. Do not rely on `$0` there to identify the eventual output window. The argument string has a 10240-byte buffer, so unusually long text can be truncated before template expansion.

Log mangling happens before rewriting. Text introduced by the rewrite is not passed through that same mangling step again. An empty expansion writes an empty line; it does not skip writing the record. Start/end logfile markers are written directly and do not use this template.

## Expansion and effects

Preserve dollar signs when assigning a template for later use. Normal command parsing may otherwise substitute `$1-` or function calls while executing `set`, before the logging routine supplies the intended arguments.

If template expansion causes more output, the logging routine refuses recursive log writes. Such nested output may appear locally without being recorded by this path. Keep rewrite functions free of unnecessary output or side effects.

Changing the setting affects subsequent writes, does not rewrite existing file contents or enable logging, and sends no IRC request.

## Examples

At the interactive command line, prefix later log lines with `+++`:

```text
/set log_rewrite +++$$1-
```

Use a user-defined formatting function with the window argument where available:

```text
/set log_rewrite $$myfunc($$0) $$1-
```

The function must already exist. Additional quoting can be needed when embedding these assignments in another expansion layer. Disable the fallback with `/set -log_rewrite`.

## Source references

- [source/log.c](../../source/log.c): `add_to_log` applies mangling, selects a specific rewrite or the global fallback, constructs arguments, expands, and writes a newline; recursion is blocked.
- [source/output.c](../../source/output.c): global session logging occurs before window routing and passes no destination window.
- [source/screen.c](../../source/screen.c) and [source/logfiles.c](../../source/logfiles.c): window and selective logs supply their own rewrite where configured.
- [source/vars.c](../../source/vars.c): string registration and unset default.
