# set lastlog_rewrite

## Synopsis

```text
/set lastlog_rewrite [<template>]
/set -lastlog_rewrite
```

Default: unset. With no template, [set](../commands/set.md) displays the current value. `/set -lastlog_rewrite` removes the default rewrite.

## Description

Provides the default template for [lastlog](../commands/lastlog.md)'s `-rewrite` option. Each `lastlog` invocation starts with this value; an explicit `-rewrite` argument replaces it for that invocation. It formats recalled output, not messages as they are originally recorded.

The template is expanded separately for each selected record, including displayed context records, in either normal or reverse order. It also applies to selected records written through `lastlog -file`. Header, footer, and context-separator lines do not use this record template.

## Template arguments

| Argument | Meaning |
| --- | --- |
| `$0` | Lastlog record refnum. |
| `$1` | Record creation time as epoch seconds. |
| `$2` | Record window's user-visible refnum. |
| `$3` | Numeric output level. |
| `$4` through `$6` | Reserved placeholders, each `.`. |
| `$7` | Recorded output target, or `.` when absent. |
| `$8-` | Selected output text. |

The template receives these fields as its argument string. The implementation constructs that string in a 10240-byte buffer, so very long text can be truncated before expansion. Formatting does not modify the stored record, its original timestamp, or later independent recalls. An empty expansion produces an empty output line rather than deleting the stored entry.

Preserve dollar signs when assigning a template intended for later expansion. Otherwise normal command parsing can expand it immediately, before lastlog supplies the record arguments.

## Example

At the interactive command line, preserve both the function call and its later positional arguments:

```text
/set lastlog_rewrite $$strftime($$1 %T) $$8-
```

Later `lastlog` output prefixes each selected record with its stored creation time formatted as local `HH:MM:SS`, followed by its text. Additional quoting may be needed when this assignment is embedded in another script-expansion layer. Disable the default with `/set -lastlog_rewrite`.

Changing the setting sends no IRC request and does not immediately redisplay lastlog.

## History

This setting first appeared in EPIC5-0.3.6.

## Source references

- [source/lastlog.c](../../source/lastlog.c): `lastlog` reads the default, handles explicit `-rewrite`, constructs record arguments, and expands the template before screen or file output.
- [source/vars.c](../../source/vars.c) and [include/config.h](../../include/config.h): string registration, standard unset handling, and unset default.
- [source/expr.c](../../source/expr.c): `$$` preserves a dollar sign for later expansion.
