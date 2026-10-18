# set debug

## Synopsis

```text
/set debug [<level>]
```

Default: `0` (off). With no level, [set](../commands/set.md) displays the current value. Supply a non-negative integer bitmask; negative or nonnumeric values are rejected.

## Description

Enables script-execution diagnostics useful for locating problems in aliases, expressions, and function calls. Combine classes by adding their values:

| Bit | Value | Diagnostics |
| --- | --- | --- |
| 0 | `1` | Statements being executed, with execution nesting depth. |
| 1 | `2` | Expando substitutions and selected control-flow expression expansions. |
| 2 | `4` | Function names, expanded arguments, and returned values. |

A value of `0` disables these classes. `3` enables statement and expansion tracing; `7` enables all three defined classes. Higher bits have no defined effect in this setting. This integer mask is separate from the named internal categories managed by [xdebug](../commands/xdebug.md).

Statement tracing occurs at entry to statement processing, before later command expansion and dispatch. Expansion tracing reports strings processed by the expando engine; it is not a comprehensive variable-assignment audit. Function tracing reports completed calls through the normal function dispatcher rather than every internal C function.

## Output and limits

The normal tracing messages use privileged diagnostic output. They bypass [on yell](../on/yell.md), ordinary display suppression, and [on window](../on/window.md) filtering; do not expect those hooks to silence them. Disable the relevant bits or set `debug` to zero to stop tracing. Other diagnostics from failing commands can use their own output paths.

Tracing can produce substantial output and exposes expanded arguments and return values in local output and applicable logs. Use the desired classes while investigating a script, then turn them off. [Window hold_mode](../window/hold_mode.md) can make extensive output easier to inspect.

Changing this setting affects subsequent tracing immediately and sends no IRC request by itself. A function call captures its tracing mask at entry, so changing the setting during that call need not change whether its return is reported.

## Examples

Trace statements and substitutions:

```text
/set debug 3
```

Trace all three defined classes, then disable them:

```text
/set debug 7
/set debug 0
```

## Source references

- [include/vars.h](../../include/vars.h): defines the three mask bits.
- [source/commands.c](../../source/commands.c): `parse_statement` emits statement and nesting diagnostics.
- [source/expr.c](../../source/expr.c) and [source/if.c](../../source/if.c): expansion and control-flow diagnostics.
- [source/functions.c](../../source/functions.c): function dispatch captures the mask and reports arguments and results.
- [source/output.c](../../source/output.c): `privileged_yell` bypasses ordinary hook-based suppression.
- [source/vars.c](../../source/vars.c): integer registration, validation, and default zero.
